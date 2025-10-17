Essential command <mark>smtp-user-enum</mark>

An essential function of an SMTP server is preventing spam using authentication mechanisms that allow only authorized users to send e-mails. For this purpose, most modern SMTP servers support the protocol extension ESMTP with SMTP-Auth. After sending his e-mail, the SMTP client, also known as `Mail User Agent` (`MUA`), converts it into a header and a body and uploads both to the SMTP server. This has a so-called `Mail Transfer Agent` (`MTA`), the software basis for sending and receiving e-mails. The MTA checks the e-mail for size and spam and then stores it. To relieve the MTA, it is occasionally preceded by a `Mail Submission Agent` (`MSA`), which checks the validity, i.e., the origin of the e-mail. This `MSA` is also called `Relay` server. These are very important later on, as the so-called `Open Relay Attack` can be carried out on many SMTP servers due to incorrect configuration. We will discuss this attack and how to identify the weak point for it a little later. The MTA then searches the DNS for the IP address of the recipient mail server.

On arrival at the destination SMTP server, the data packets are reassembled to form a complete e-mail. From there, the `Mail delivery agent` (`MDA`) transfers it to the recipient's mailbox.

|Client (`MUA`)|`➞`|Submission Agent (`MSA`)|`➞`|Open Relay (`MTA`)|`➞`|Mail Delivery Agent (`MDA`)|`➞`|Mailbox (`POP3`/`IMAP`)|
|---|---|---|---|---|---|---|---|---|

But SMTP has two disadvantages inherent to the network protocol.

1. The first is that sending an email using SMTP does not return a usable delivery confirmation. Although the specifications of the protocol provide for this type of notification, its formatting is not specified by default, so that usually only an English-language error message, including the header of the undelivered message, is returned.
    
2. Users are not authenticated when a connection is established, and the sender of an email is therefore unreliable. As a result, open SMTP relays are often misused to send spam en masse. The originators use arbitrary fake sender addresses for this purpose to not be traced (mail spoofing). Today, many different security techniques are used to prevent the misuse of SMTP servers. For example, suspicious emails are rejected or moved to quarantine (spam folder). For example, responsible for this are the identification protocol [DomainKeys](http://dkim.org/) (`DKIM`), the [Sender Policy Framework](https://dmarcian.com/what-is-spf/) (`SPF`).
    

For this purpose, an extension for SMTP has been developed called `Extended SMTP` (`ESMTP`). When people talk about SMTP in general, they usually mean ESMTP. ESMTP uses TLS, which is done after the `EHLO` command by sending `STARTTLS`. This initializes the SSL-protected SMTP connection, and from this moment on, the entire connection is encrypted, and therefore more or less secure. Now [AUTH PLAIN](https://www.samlogic.net/articles/smtp-commands-reference-auth.htm) extension for authentication can also be used safely.

The sending and communication are also done by special commands that cause the SMTP server to do what the user requires.

|Command|	Description|
|-|-|
|AUTH PLAIN |	AUTH is a service extension used to authenticate the client.|
|HELO |	The client logs in with its computer name and thus starts the session.|
|MAIL FROM |	The client names the email sender.|
|RCPT TO |	The client names the email recipient.|
|DATA |	The client initiates the transmission of the email.|
|RSET |	The client aborts the initiated transmission but keeps the connection between client and server.|
|VRFY |	The client checks if a mailbox is available for message transfer.|
|EXPN |	The client also checks if a mailbox is available for messaging with this command.|
|NOOP |	The client requests a response from the server to prevent disconnection due to time-out.|
|QUIT |	The client terminates the session.|

To interact with the SMTP server, we can use the telnet tool to initialize a TCP connection with the SMTP server. The actual initialization of the session is done with the command mentioned above, HELO or EHLO.
##### Telnet - HELO/EHLO
``` bash
astrocat@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

HELO mail1.inlanefreight.htb
250 mail1.inlanefreight.htb

EHLO mail1
250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING

VRFY root
252 2.0.0 root

VRFY cry0l1t3
252 2.0.0 cry0l1t3

EHLO inlanefreight.htb

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING

MAIL FROM: <cry0l1t3@inlanefreight.htb>
250 2.1.0 Ok

RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure
250 2.1.5 Ok

DATA
354 End data with <CR><LF>.<CR><LF>

From: <cry0l1t3@inlanefreight.htb>
To: <mrb3n@inlanefreight.htb>
Subject: DB
Date: Tue, 28 Sept 2021 16:32:51 +0200
Hey man, I am trying to access our XY-DB but the creds don\'t work. 
Did you \make any changes there?
.

250 2.0.0 Ok: queued as 6E1CF1681AB

QUIT
221 2.0.0 Bye
Connection closed by foreign host.
```

The command `VRFY` can be used to enumerate existing users on the system. However, this does not always work. Depending on how the SMTP server is configured, the SMTP server may issue `code 252` and confirm the existence of a user that does not exist on the system. A list of all SMTP response codes can be found [here](https://serversmtp.com/smtp-error/)
We can also use the smtp-open-relay NSE script to identify the target SMTP server as an open relay using 16 different tests. If we also print out the output of the scan in detail, we will also be able to see which tests the script is running.

Nmap - Open Relay
``` bash
astrocat@htb[/htb]$ sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-30 02:29 CEST
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Initiating ARP Ping Scan at 02:29
Scanning 10.129.14.128 [1 port]
Completed ARP Ping Scan at 02:29, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 02:29
Completed Parallel DNS resolution of 1 host. at 02:29, 0.03s elapsed
Initiating SYN Stealth Scan at 02:29
Scanning 10.129.14.128 [1 port]
Discovered open port 25/tcp on 10.129.14.128
Completed SYN Stealth Scan at 02:29, 0.06s elapsed (1 total ports)
NSE: Script scanning 10.129.14.128.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.07s elapsed
Nmap scan report for 10.129.14.128
Host is up (0.00020s latency).

PORT   STATE SERVICE
25/tcp open  smtp
| smtp-open-relay: Server is an open relay (16/16 tests)
|  MAIL FROM:<> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@nmap.scanme.org> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@ESMTP> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest%nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org"@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@[10.129.14.128]:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@ESMTP:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@[10.129.14.128]>
|_ MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@ESMTP>
MAC Address: 00:00:00:00:00:00 (VMware)

NSE: Script Post-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
           Raw packets sent: 2 (72B) | Rcvd: 2 (72B)
```
