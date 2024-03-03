# Reconnaissance
## Search for Active Host
**PING**
```
#!/bin/bash

subnet="10.10.110"
network="$subnet.0/24"

echo "Scanning hosts in $network"

for host in $(seq 1 254); do
    ip="$subnet.$host"
    ping -c 1 -w 1 $ip > /dev/null
    if [ $? -eq 0 ]; then
        echo "Host $ip is up"
    fi
done
```

**RESULT**
```
$ ./scan-active.sh      
Scanning hosts in 10.10.110.0/24
Host 10.10.110.2 is up
Host 10.10.110.100 is up
```

## Port Scanning
**NMAP**
```
$ nmap -A -sC -p- -Pn 10.10.110.100 -oN active-hosts 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-01 21:43 EST
Nmap scan report for 10.10.110.100
Host is up (0.20s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV IP 172.16.1.100 is not the same as 10.10.110.100
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 8f:a2:ff:cf:4e:3e:aa:2b:c2:6f:f4:5a:2a:d9:e9:da (RSA)
|   256 07:83:8e:b6:f7:e6:72:e9:65:db:42:fd:ed:d6:93:ee (ECDSA)
|_  256 13:45:c5:ca:db:a6:b4:ae:9c:09:7d:21:cd:9d:74:f4 (ED25519)
65000/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
| http-robots.txt: 2 disallowed entries 
|_/wordpress DANTE{Y0u_Cant_G3t_at_m3_br0!}
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 429.22 seconds
```
