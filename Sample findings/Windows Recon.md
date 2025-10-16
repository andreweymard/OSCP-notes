``` bash
sudo nmap -top-ports 2000 10.129.230.148 -Pn -sC -T5 -sV 

Nmap scan report for 10.129.230.148                                                                   
Host is up (0.29s latency).                                                
Not shown: 1992 closed tcp ports (reset)                                                                                                                                                                           
PORT     STATE SERVICE        VERSION                                       
22/tcp   open  ssh            OpenSSH for_Windows_9.5 (protocol 2.0)        

135/tcp  open  msrpc          Microsoft Windows RPC                      

139/tcp  open  netbios-ssn    Microsoft Windows netbios-ssn          

445/tcp  open  microsoft-ds   Windows Server 2019 Standard 17763 microsoft-ds      

3000/tcp open  ppp?                                                                                                
| fingerprint-strings:                                                       
|   GenericLines, Help:                                                     
|     HTTP/1.1 400 Bad Request                                             
|     Content-Type: text/plain; charset=utf-8                              
|     Connection: close                                                               
|     Request                                                                                  
|   GetRequest:                                                                            
|     HTTP/1.0 200 OK                                                                  
|     Content-Type: text/html; charset=UTF-8                                 
|     Set-Cookie: lang=en-US; Path=/; Max-Age=2147483647                   
|     Set-Cookie: i_like_gitea=b4714e1f53675e93; Path=/; HttpOnly           
|     Set-Cookie: _csrf=WNEttO-xvhT7wrw3rdukD_l28p86MTc1OTI0NjA0OTE0NTIwMzkwMA; Path=/; Expires=Wed, 01 Oct 2025 15:27:29 GMT; HttpOnly                                                                            
|     X-Frame-Options: SAMEORIGIN                                                       
|     Date: Tue, 30 Sep 2025 15:27:29 GMT                                            
|     <!DOCTYPE html>                                                                           
|     <html lang="en-US" class="theme-">                                              
|     <head data-suburl="">                                                                    
|     <meta charset="utf-8">                                                 
|     <meta name="viewport" content="width=device-width, initial-scale=1">       
|     <meta http-equiv="x-ua-compatible" content="ie=edge">                           
|     <title> Cube Case Gitea </title>                                                                      
|     <link rel="manifest" href="/manifest.json" crossorigin="use-credentials">           
|     <meta name="theme-color" content="#6cc644">                                                  
|     <meta name="author" content="Gitea - Git with a cup of tea" />                            
|     <meta name="description" content="Gitea (Git with a cup of tea) is a painless self-hosted"                                                                                                                    
|   HTTPOptions:                                                                
|     HTTP/1.0 404 Not Found                                                 
|     Content-Type: text/html; charset=UTF-8                                                   
|     Set-Cookie: lang=en-US; Path=/; Max-Age=2147483647                      
|     Set-Cookie: i_like_gitea=81f1b4889babfb3e; Path=/; HttpOnly               
|     Set-Cookie: _csrf=o0rnPQA2tJOVMlTgqyYni7GJO2I6MTc1OTI0NjA1NzQxMzE5NjcwMA; Path=/; Expires=Wed, 01 Oct 2025 15:27:37 GMT; HttpOnly
|     X-Frame-Options: SAMEORIGIN
|     Date: Tue, 30 Sep 2025 15:27:37 GMT
|     <!DOCTYPE html>                               
|     <html lang="en-US" class="theme-">
|     <head data-suburl="">
|     <meta charset="utf-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1">     
|     <meta http-equiv="x-ua-compatible" content="ie=edge">                                
|     <title>Page Not Found - Cube Case Gitea </title>                                                   
|     <link rel="manifest" href="/manifest.json" crossorigin="use-credentials">        
|     <meta name="theme-color" content="#6cc644">
|     <meta name="author" content="Gitea - Git with a cup of tea" />                        
|_    <meta name="description" content="Gitea (Git with a cup of tea) is "                                
3389/tcp open  ms-wbt-server  Microsoft Terminal Services                      
| rdp-ntlm-info:                                    
|   Target_Name: WIN01                              
|   NetBIOS_Domain_Name: WIN01
|   NetBIOS_Computer_Name: WIN01
|   DNS_Domain_Name: WIN01
|   DNS_Computer_Name: WIN01
|   Product_Version: 10.0.17763
|_  System_Time: 2025-09-30T15:29:48+00:00
| ssl-cert: Subject: commonName=WIN01
| Not valid before: 2025-09-29T15:13:17
|_Not valid after:  2026-03-31T15:13:17
|_ssl-date: 2025-09-30T15:30:05+00:00; 0s from scanner time.                                             
8000/tcp open  ssl/http       Golang net/http server (Go-IPFS json-rpc or InfluxDB API)    
| ssl-cert: Subject: commonName=VelociraptorServer/organizationName=Velociraptor
| Subject Alternative Name: DNS:VelociraptorServer, DNS:VelociraptorServer          
| Not valid before: 2024-09-02T12:20:15
|_Not valid after:  2034-08-31T12:20:15
|_ssl-date: TLS randomness does not represent time
|_http-title: Site doesn\'t have a title (text/plain; charset=utf-8).        

8889/tcp open  ssl/ddi-tcp-2?
| ssl-cert: Subject: commonName=VelociraptorServer/organizationName=Velociraptor 
| Subject Alternative Name: DNS:VelociraptorServer, DNS:VelociraptorServer    
| Not valid before: 2024-09-02T12:20:15
|_Not valid after:  2034-08-31T12:20:15
|_ssl-date: TLS randomness does not represent time
| fingerprint-strings:                              
|   FourOhFourRequest:                              
|     HTTP/1.0 302 Found                            
|     Content-Type: text/html; charset=utf-8
|     Location: /app/index.html
|     Date: Tue, 30 Sep 2025 15:28:29 GMT
|     Content-Length: 38                            
|     href="/app/index.html">Found</a>.
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close                             
|     Request                                       
|   GetRequest:                                     
|     HTTP/1.0 302 Found                            
|     Content-Type: text/html; charset=utf-8
|     Location: /app/index.html
|     Date: Tue, 30 Sep 2025 15:27:44 GMT
|     Content-Length: 38                            
|     href="/app/index.html">Found</a>.
|   HTTPOptions:                                    
|     HTTP/1.0 302 Found                            
|     Location: /app/index.html
|     Date: Tue, 30 Sep 2025 15:27:46 GMT
|_    Content-Length: 0
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows                 

Host script results:                                
| smb-os-discovery:                                 
|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)     
|   Computer name: WIN01                            
|   NetBIOS computer name: WIN01\x00
|   Workgroup: HTBLAB\x00                           
|_  System time: 2025-09-30T10:29:48-05:00
| sm b-security-mode:                                
|   account_used: guest                             
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)                                                   
| smb2-time:                                        
|   date: 2025-09-30T15:29:49
|_  start_date: N/A                                 
|_clock-skew: mean: 1h00m00s, deviation: 2h14m10s, median: 0s      
| smb2-security-mode:                               
|   3:1:1:                                          
|_    Message signing enabled but not required

Nmap done: 1 IP address (1 host up) scanned in 177.25 seconds
```

```shell-session
crackmapexec smb 10.129.12.20 -u guest -p '' --shares
netexec smb 10.129.230.148 -u anonymous -p anonymous --shares
```
SMB         10.129.230.148  445    WIN01            [*] Windows Server 2019 Standard 17763 x64 (name:WIN01) (domain:WIN01) (signing:False) (SMBv1:True)
SMB         10.129.230.148  445    WIN01            [+] WIN01\anonymous:anonymous (Guest)
SMB         10.129.230.148  445    WIN01            [*] Enumerated shares
SMB         10.129.230.148  445    WIN01            Share           Permissions     Remark
SMB         10.129.230.148  445    WIN01            -----           -----------     ------
SMB         10.129.230.148  445    WIN01            ADMIN$                          Remote Admin
SMB         10.129.230.148  445    WIN01            C$                              Default share
SMB         10.129.230.148  445    WIN01            Devs                            
SMB         10.129.230.148  445    WIN01            IPC$                            Remote IPC

