## Target: 10.129.12.10

Let’s pick the 10.129.12.10 target and examine TCP ports 21 and 443 first. These two TCP ports are used quite often, and is common for them misconfigured or even vulnerable to certain attacks. For now, we do not need to do anything fancy, and can just work with what we see.

Low Hanging Fruits

```shell-session
astrocat@htb[/htb]$ sudo nmap -p21,22,443 -sV -sC 10.129.12.10

Starting Nmap 7.95 ( <https://nmap.org> ) at 2025-02-15 15:29 EST
Nmap scan report for cube-case.htb (10.129.12.10)
Host is up (0.027s latency).

PORT    STATE SERVICE
21/tcp  open  ftp      ProFTPD
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-rw-r--   1 john     john          964 Feb 15 22:14 WordPress_Blog_Setup_Update.txt
22/tcp  open  ssh      OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
443/tcp open  https
|_http-title: Cube Case
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=cube-case.htb/organizationName=Organization/stateOrProvinceName=State/countryName=US
| Not valid before: 2025-02-15T18:00:13
|_Not valid after:  2026-02-15T18:00:13
| tls-alpn:
|_  http/1.1
|_http-generator: WordPress 6.7.2

Nmap done: 1 IP address (1 host up) scanned in 4.98 seconds
```

From the results of the scan, we can see that Nmap discovered anonymous access for the FTP port TCP/21. Administrators might use or allow anonymous FTP login in their business infrastructure to simplify file sharing for external users, or the public (using it to distribute software updates, manuals, or open datasets.) It eliminates the need for individual user accounts, reducing management overhead, especially in scenarios where authentication isn’t critical and the data isn’t sensitive.

However, if an administrator configures the FTP server for internal use in a way that inadvertently exposes it to the public, it can lead to data exposure and unauthorized access to internal data.

So, we've now obtained several important pieces of information that we can benefit from. We see that there is a file called `WordPress_Blog_Setup_Update.txt` , and also know there is a user and group called `john` . Information like this should definitely be written down, as it may become useful to us later on.

On TCP port 443, we see a webserver using TLS/SSL with the title `Cube Case` . The `ssl-certificate` subject contains the domain name `cube-case.htb` , and shows that it’s using WordPress CMS with the version 6.7.2. When we visit the website `https://10.129.12.10` we see a sample WordPress page. A sample page serves as an initial placeholder (like the default settings when you buy a smartphone) and can indicate several things; either it has been installed recently, or admins forgot about it and left it as it is. Incidentally, both cases can lead to breach for a business.

![Cube Case blog page with 'Hello world!' post, welcoming users to WordPress, dated February 15, 2025.](https://academy.hackthebox.com/storage/modules/296/nix-wordpress.png)

Now, let’s summarize what we have figured out so far:

- Operating System: Ubuntu
- File sharing: FTP on 21
- Remote Access: SSH on 22
- Web Server: WordPress 6.7.2
- Domain: cube-case.htb

This is sufficient for now, as we know what types of systems we will be dealing with moving forward. WordPress has many attack vectors that require some time to assess, and this will be the part of information gathering and vulnerability assessment stages (which we will jump into shortly). So, at this moment, we will focus on the next target with the IP address 10.129.12.20.

---

## Target: 10.129.12.20

Let’s use another scan method for this target which includes version detection and script application for the running services.

Low Hanging Fruits

```shell-session
astrocat@htb[/htb]$ sudo nmap -p- -sV -sC 10.129.12.20 -T5 -Pn

Starting Nmap 7.95 ( <https://nmap.org> ) at 2025-02-23 16:06 EST
Nmap scan report for 10.129.12.20
Host is up (0.029s latency).
Not shown: 65523 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
22/tcp    open  ssh           OpenSSH for_Windows_9.5 (protocol 2.0)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server 2019 Standard 17763 microsoft-ds
3000/tcp  open  http          Golang net/http server
|_http-title:  Cube Case Gitea
| fingerprint-strings:
|   GenericLines, Help, RTSPRequest:
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest:
|     HTTP/1.0 200 OK
|     Content-Type: text/html; charset=UTF-8
|     Set-Cookie: i_like_gitea=e395a074552264a2; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=uZ6gm5xTwEmOsxJTg4e-1VuKws06MTc0MDM0NDg4MzEwNTMzMjgwMA; Path=/; Expires=Mon, 24 Feb 2025 21:08:03 GMT; HttpOnly; SameSite=Lax
|     Set-Cookie: macaron_flash=; Path=/; Max-Age=0; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sun, 23 Feb 2025 21:08:03 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-">
|     <head>
|     <meta charset="utf-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title> Cube Case Gitea</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiQ3ViZSBDYXNlIEdpdGVhIiwic2hvcnRfbmFtZSI6IkN1YmUgQ2FzZSBHaXRlYSIsInN0YXJ0X3VybCI6Imh0dHA6Ly8wLjAuMC4wOjMwMDAvIiwiaWNvbnMiOlt7InNyYyI6Imh0dHA6Ly8wLjAuMC4wOjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLCJzaXplcyI6IjUxMn
|   HTTPOptions:
|     HTTP/1.0 405 Method Not Allowed
|     Set-Cookie: i_like_gitea=7c2f23bd7c3f6266; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=snXz9bwqPGeywxtPwsHN6nXb3es6MTc0MDM0NDg4MzQyNjkzMjgwMA; Path=/; Expires=Mon, 24 Feb 2025 21:08:03 GMT; HttpOnly; SameSite=Lax
|     Set-Cookie: macaron_flash=; Path=/; Max-Age=0; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sun, 23 Feb 2025 21:08:03 GMT
|_    Content-Length: 0
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-02-23T21:09:33+00:00; +4s from scanner time.
| ssl-cert: Subject: commonName=WIN01
| Not valid before: 2025-02-22T20:56:06
|_Not valid after:  2025-08-24T20:56:06
5357/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
<SNIP>
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
<SNIP>
5986/tcp  open  ssl/http      Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
<SNIP>
8000/tcp  open  ssl/http      Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
<SNIP>
8889/tcp  open  ssl/http      Golang net/http server
<SNIP>
49669/tcp open  msrpc         Microsoft Windows RPC
<SNIP>
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery:
|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
|   Computer name: WIN01
|   NetBIOS computer name: WIN01\\x00
|   Workgroup: HTBLAB\\x00
|_  System time: 2025-02-23T15:08:55-06:00
| smb2-security-mode:
|   3:1:1:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2025-02-23T21:08:54
|_  start_date: N/A
|_clock-skew: mean: 1h12m04s, deviation: 2h41m01s, median: 3s

Service detection performed. Please report any incorrect results at <https://nmap.org/submit/> .
Nmap done: 1 IP address (1 host up) scanned in 157.72 seconds

```

Now, let's examine the results and write down any juicy information.

- Hostname: WIN01
- Operating System: Windows Server 2019 Standard (build 17763)
- File sharing: SMB on 445
- Remote Access: SSH on 22, RDP on 3389
- Web Server: Gitea
- Domain: cube-case.htb

If we visit the [official documentation page](https://docs.gitea.com/) of Gitea, we will see the version of the most recent release. If we compare it to the one found on our target, we will see that they are using an outdated version of Gitea. It's very likely that there is some type of vulnerability or security gap, which has since been patched in the latest version, but may be present on our target.

---

## Summary

Let's summarize our findings from the low-hanging fruit reconnaissance of both targets:

#### Target 1 (10.129.12.10)

- Running Ubuntu operating system
- FTP service on port 21
- SSH access on port 22
- WordPress 6.7.2 installation
- Domain: cube-case.htb

Key findings: WordPress installation may have multiple attack vectors that require further assessment.

#### Target 2 (10.129.12.20)

- Windows Server 2019 Standard (build 17763)
- Hostname: WIN01
- Domain: cube-case.htb
- Multiple services running:
    - SMB file sharing (port 445)
    - SSH (port 22)
    - RDP (port 3389)
    - Gitea installation (port 3000)

Key findings: Outdated Gitea installation that may contain vulnerabilities and potentially sensitive information.