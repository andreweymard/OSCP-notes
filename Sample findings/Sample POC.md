Let\u2019s take an example and create a PoC for the first stage on the Linux target, keeping in mind that a complete PoC should cover all steps from start to finish.
Linux Target - Information Gathering

``` bash

# 0. Summary

### FTP
- Access and Environment
- Anonymous FTP access was enabled on port 21
- We identified this as a home directory for user john
- Several important configuration and history files were accessible

### Critical Files
- .bash_history file revealed potential credentials: john:SuperSecurePass123
- SSH private key (id_rsa) was obtained and is not password protected
- WordPress setup documentation revealed temporary FTP configuration

### Security Implications
- Exposed SSH private key could allow unauthorized system access
- FTP server was intended to be temporary but remained accessible
- Internal documentation exposed system configuration details
- Command history revealed sensitive operations and potential credentials

### WordPress
- Running WordPress version 6.7.2 (Latest version)
- Server: Apache/2.4.52 (Ubuntu)
- XML-RPC enabled and accessible
- Theme: twentytwentyfive v1.0
- Plugin: hash-form v1.1.0 - vulnerable to RCE (CVE-2024-5084)

# ----------------------------------------------------------------------------------
# 1. Port & Service Discovery
sudo nmap -p- -sV 10.129.12.10 -T5 --open

Starting Nmap 7.95 ( <https://nmap.org> ) at 2025-02-15 14:55 EST
Nmap scan report for cube-case.htb (10.129.12.10)
Host is up (0.048s latency).
Not shown: 65527 closed tcp ports (reset)
PORT     STATE SERVICE          VERSION
21/tcp   open  ftp              ProFTPD
22/tcp   open  ssh              OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http             nginx 1.18.0 (Ubuntu)
443/tcp  open  ssl/http         Apache httpd 2.4.52 ((Ubuntu))
8000/tcp open  ssl/http         Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
8001/tcp open  ssl/vcom-tunnel?
8080/tcp open  http             Apache httpd 2.4.52 ((Ubuntu))
8889/tcp open  ssl/http         Golang net/http server
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at <https://nmap.org/submit/> .
Nmap done: 1 IP address (1 host up) scanned in 61.99 seconds

# 2. Low Hanging Fruits
sudo nmap -p21,443 -sC 10.129.12.10

Starting Nmap 7.95 ( <https://nmap.org> ) at 2025-02-15 15:29 EST
Nmap scan report for cube-case.htb (10.129.12.10)
Host is up (0.027s latency).

PORT    STATE SERVICE
21/tcp  open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxr-x   3 john     john         4096 Feb 15 20:06 Dev

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

# 3. WordPress Enumeration
wpscan -e p --url <https://10.129.12.10> --disable-tls-checks --no-banner --plugins-detection passive -t 100

<SNIP>
[+] hash-form
 | Location: <https://cube-case.htb/wp-content/plugins/hash-form/>
 | Last Updated: 2025-01-29T15:54:00.000Z
 | [!] The version is out of date, the latest version is 1.2.4
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Urls In 404 Page (Passive Detection)
 |
 | Version: 1.1.0 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - <https://cube-case.htb/wp-content/plugins/hash-form/readme.txt>
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)
 |  - <https://cube-case.htb/wp-content/plugins/hash-form/readme.txt>

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at <https://wpscan.com/register>

[+] Finished: Sat Feb 15 15:36:47 2025
[+] Requests Done: 109150
[+] Cached Requests: 13
[+] Data Sent: 29.515 MB
[+] Data Received: 14.722 MB
[+] Memory used: 545.891 MB
[+] Elapsed time: 00:01:54

# 4. FTP Enumeration
[!bash!]$ ftp 10.129.12.10 21

Connected to 10.129.12.10.
220 ProFTPD Server (Debian) [10.129.12.10]
Name (10.129.12.10:pwnbox): anonymous

331 Anonymous login ok, send your complete email address as your password
Password: anonymous

230 Anonymous access granted, restrictions apply
Remote system type is UNIX.
Using binary mode to transfer files.

ftp> ls -al

229 Entering Extended Passive Mode (|||42851|)
150 Opening ASCII mode data connection for file list
drwxr-x---   4 john     john         4096 Feb 16 17:28 .
drwxr-x---   4 john     john         4096 Feb 16 17:28 ..
-rw-------   1 john     john         1153 Feb 15 21:13 .bash_history
-rw-r--r--   1 john     john          220 Jan  6  2022 .bash_logout
-rw-r--r--   1 john     john         3771 Jan  6  2022 .bashrc
drwx------   2 john     john         4096 Feb 15 17:16 .cache
-rw-------   1 john     john           20 Feb 16 16:34 .lesshst
-rw-r--r--   1 john     john          807 Jan  6  2022 .profile
drwxrwxr-x   2 john     john         4096 Feb 12 13:55 .ssh
-rw-r--r--   1 john     john            0 Feb 11 10:18 .sudo_as_admin_successful
-rw-------   1 john     john        17094 Feb 15 22:14 .viminfo
-rw-rw-r--   1 john     john          964 Feb 15 22:14 WordPress_Blog_Setup_Update.txt
226 Transfer complete

ftp> get WordPress_Blog_Setup_Update.txt

local: WordPress_Blog_Setup_Update.txt remote: WordPress_Blog_Setup_Update.txt
229 Entering Extended Passive Mode (|||12725|)
150 Opening BINARY mode data connection for WordPress_Blog_Setup_Update.txt (964 bytes)
100% |**********************************************|   964       14.97 KiB/s    00:00 ETA
226 Transfer complete
964 bytes received in 00:00 (7.92 KiB/s)

ftp> get .bash_history

local: bash_history remote: bash_history
229 Entering Extended Passive Mode (|||39373|)
150 Opening BINARY mode data connection for bash_history (1285 bytes)
100% |**********************************************|  1285       51.01 KiB/s    00:00 ETA
226 Transfer complete
1285 bytes received in 00:00 (10.95 KiB/s)

ftp> cd .ssh
ftp> ls -al

229 Entering Extended Passive Mode (|||26184|)
150 Opening ASCII mode data connection for file list
-rw-------   1 john     john         2602 Feb 12 13:55 id_rsa
-rw-r--r--   1 john     john          565 Feb 12 13:55 id_rsa.pub
226 Transfer complete

ftp> get id_rsa

local: id_rsa remote: id_rsa
229 Entering Extended Passive Mode (|||41007|)
150 Opening BINARY mode data connection for id_rsa (2602 bytes)
100% |**********************************************|  2602      108.80 KiB/s    00:00 ETA
226 Transfer complete
2602 bytes received in 00:00 (22.28 KiB/s)

Attacker@Pwnbox:~$ cat WordPress_Blog_Setup_Update.txt

**Subject: Update on WordPress Blog Setup**

Dear Team,

We are currently in the process of setting up a new blog using WordPress.
To facilitate this setup, we have temporarily configured an FTP server.

Please note:
- This FTP server is for internal use only during the initial setup phase.
- Access credentials are temporary and will be revoked once the blog setup is complete or if security necessitates it.

However, we have recently received a high-priority ticket that requires immediate attention. As a result,
the completion of the blog setup will be slightly delayed. We are committed to addressing this urgent issue first to ensure our services remain uninterrupted.

We will notify the responsible parties as soon as the WordPress blog and web application are fully operational.
We appreciate your understanding and patience as we work to prioritize our tasks effectively.

Thank you for your cooperation.

Best Regards,
John Doe
Development Team

Attacker@Pwnbox:~$ cat .bash_history

<SNIP>
sudo rm -f /tmp/old_log.txt
sudo find /var/log -type f -name "*.gz" -delete
sudo systemctl restart rsyslog
cd /var/backups
ls -la
sudo tar -czf full_backup.tar.gz /etc /var/log
sudo mv full_backup.tar.gz /mnt/backup/
cd /mnt/backup
echo "john:SuperSecurePass123" | sudo chpasswd
<SNIP>

Attacker@Pwnbox:~$ cat id_rsa

-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
<SNIP>
kRnXtk6X8U1DNfAAAAC2pvaG5AdWJ1bnR1AQIDBAUGBw==
-----END OPENSSH PRIVATE KEY-----
```

