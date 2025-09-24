Let’s consider a practical example of scanning the 10.129.12.0/24 network that's been assigned to us, which contains only two hosts. To perform the scan, we can use the following Nmap command:

Network and Service Scanning

```shell-session
astrocat@htb[/htb]$ nmap -sV -p- 10.129.12.0/24 -oA network-scan
```

- `sV`: Enables service version detection, which attempts to identify the software and version running on each open port.
    
- `-p-`: Scans all 65,535 ports (both TCP and UDP) to ensure no services are missed.
    
- `-oA network-scan`: Save the results of the scan to a file called "network-scan".
    
- `10.129.12.0/24`: Specifies the target network range.
    

Assuming the scan identifies the two hosts, the output might look like this (simplified for clarity):

Network and Service Scanning

```shell-session
astrocat@htb[/htb]$ nmap -sV -p- 10.129.12.0/24 -oA network-scan

Starting Nmap 7.94 ( <https://nmap.org> )
Nmap scan report for 10.129.12.10
Host is up (0.0012s latency).
PORT     STATE SERVICE          VERSION
21/tcp   open  ftp              ProFTPD
22/tcp   open  ssh              OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http             nginx 1.18.0 (Ubuntu)
443/tcp  open  ssl/http         Apache httpd 2.4.52 ((Ubuntu))
8000/tcp open  ssl/http         Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
8001/tcp open  ssl/vcom-tunnel?
8080/tcp open  http             Apache httpd 2.4.52 ((Ubuntu))
8889/tcp open  ssl/http         Golang net/http server

Nmap scan report for 10.129.12.20
Host is up (0.0015s latency).
PORT     STATE SERVICE         VERSION
22/tcp    open  ssh            OpenSSH for_Windows_9.5 (protocol 2.0)
135/tcp   open  msrpc          Microsoft Windows RPC
139/tcp   open  netbios-ssn    Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds   Windows Server 2019 Standard 17763 microsoft-ds
3000/tcp  open  http           Golang net/http server
3389/tcp  open  ms-wbt-server  Microsoft Terminal Services
5357/tcp  open  http           Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
5985/tcp  open  http           Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
5986/tcp  open  ssl/http       Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8000/tcp  open  ssl/http       Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
8889/tcp  open  ssl/http       Golang net/http server
49669/tcp open  msrpc          Microsoft Windows RPC

Nmap done: 256 IP addresses (2 hosts up) scanned in 12.34 seconds
```

---

## Interpreting the Scan Results

The Nmap output provides a detailed snapshot of the network, highlighting the active hosts and their open ports. Let’s break down the results for each host:

#### 10.129.12.10

- `Port 21` (FTP): Running vsftpd 3.0.3, a popular FTP server. FTP is often a target for attacks due to weak authentication mechanisms or misconfigurations.
    
- `Port 22` (SSH): Running OpenSSH 8.2p1. SSH is a secure protocol, but vulnerabilities in older versions or weak credentials could be exploited.
    
- `Port 80` (HTTP): Hosting an Apache httpd 2.4.41 web server. Web servers are common attack vectors, especially if they host vulnerable applications or misconfigured settings.
    
- `Port 443` (HTTPS): Also running Apache httpd 2.4.41 with SSL/TLS. HTTPS services may be vulnerable to SSL/TLS misconfigurations or outdated cipher suites.
    
- `Port 4369` (Erlang Port Mapper Daemon): Used by Erlang-based applications. This service is less common and may indicate a specialized application, potentially misconfigured or vulnerable.
    

#### 10.129.12.20

- `Port 22` (SSH): Running OpenSSH for Windows 9.5. SSH provides secure remote access, though Windows-specific implementations may have unique security considerations.
    
- `Port 139` (NetBIOS): A legacy protocol used for file sharing and printer services. It is often targeted in attacks due to historical vulnerabilities.
    
- `Port 445` (SMB): Running Microsoft’s Server Message Block protocol, critical for file sharing in Windows environments. SMB has been a target for major exploits like EternalBlue.
    
- `Port 3000` (HTTP): Running a Golang net/http server. Web applications on non-standard ports may indicate internal services or development environments that require security review.
    
- `Port 3389` (RDP): Microsoft’s Remote Desktop Protocol, used for remote administration. RDP is frequently targeted for brute-force attacks or exploits if not properly secured.