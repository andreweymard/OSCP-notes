Remote management is enabled by default starting with Windows Server 2016. The main components used for remote management of Windows and Windows servers are the following:
    Remote Desktop Protocol (RDP)
    Windows Remote Management (WinRM)
    Windows Management Instrumentation (WMI)

## RDP
TCP port 3389 as the transport protocol. However, the connectionless UDP protocol can use port 3389 also for remote administration.
RDP has handled [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (`TLS/SSL`) since Windows Vista, which means that all data, and especially the login process, is protected in the network by its good encryption. However, many Windows systems do not insist on this but still accept inadequate encryption via [RDP Security](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/8e8b2cca-c1fa-456c-8ecb-a82fc60b2322). Nevertheless, even with this, an attacker is still far from being locked out because the identity-providing certificates are merely self-signed by default. This means that the client cannot distinguish a genuine certificate from a forged one and generates a certificate warning for the user.

The `Remote Desktop` service is installed by default on Windows servers and does not require additional external applications. This service can be activated using the `Server Manager` and comes with the default setting to allow connections to the service only to hosts with [Network level authentication](https://en.wikipedia.org/wiki/Network_Level_Authentication) (`NLA`).

``` bash
astrocat@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```
In addition, we can use `--packet-trace` to track the individual packages and inspect their contents manually. We can see that the `RDP cookies` (`mstshash=nmap`) used by Nmap to interact with the RDP server can be identified by `threat hunters` and various security services such as [Endpoint Detection and Response](https://en.wikipedia.org/wiki/Endpoint_detection_and_response) (`EDR`), and can lock us out as penetration testers on hardened networks.

``` bash
astrocat@htb[/htb]$ git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
astrocat@htb[/htb]$ ./rdp-sec-check.pl 10.129.201.248
```
Authentication and connection to such RDP servers can be made in several ways. For example, we can connect to RDP servers on Linux using xfreerdp, rdesktop, or Remmina and interact with the GUI of the server accordingly..

## WinRM

The Windows Remote Management (`WinRM`) is a simple Windows integrated remote management protocol based on the command line. WinRM uses the Simple Object Access Protocol (`SOAP`) to establish connections to remote hosts and their applications. Therefore, WinRM must be explicitly enabled and configured starting with Windows 10. WinRM relies on `TCP` ports `5985` and `5986` for communication, with the last port `5986 using HTTPS`, as ports 80 and 443 were previously used for this task. However, since port 80 was mainly blocked for security reasons, the newer ports 5985 and 5986 are used today.

Another component that fits WinRM for administration is Windows Remote Shell (`WinRS`), which lets us execute arbitrary commands on the remote system. The program is even included on Windows 7 by default. Thus, with WinRM, it is possible to execute a remote command on another server.

Services like remote sessions using PowerShell and event log merging require WinRM. It is enabled by default starting with the `Windows Server 2012` version, but it must first be configured for older server versions and clients, and the necessary firewall exceptions created.

## Footprinting the Service

As we already know, WinRM uses TCP ports `5985` (`HTTP`) and `5986` (`HTTPS`) by default, which we can scan using Nmap. However, often we will see that only HTTP (`TCP 5985`) is used instead of HTTPS (`TCP 5986`).

``` bash
nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```

If we want to find out whether one or more remote servers can be reached via WinRM, we can easily do this with the help of PowerShell. The [Test-WsMan](https://docs.microsoft.com/en-us/powershell/module/microsoft.wsman.management/test-wsman?view=powershell-7.2) cmdlet is responsible for this, and the host's name in question is passed to it. In Linux-based environments, we can use the tool called [evil-winrm](https://github.com/Hackplayers/evil-winrm), another penetration testing tool designed to interact with WinRM.
``` bash
astrocat@htb[/htb]$ evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!
```
## WMI

Windows Management Instrumentation (`WMI`) is Microsoft's implementation and also an extension of the Common Information Model (`CIM`), core functionality of the standardized Web-Based Enterprise Management (`WBEM`) for the Windows platform. WMI allows read and write access to almost all settings on Windows systems. Understandably, this makes it the most critical interface in the Windows environment for the administration and remote maintenance of Windows computers, regardless of whether they are PCs or servers. WMI is typically accessed via PowerShell, VBScript, or the Windows Management Instrumentation Console (`WMIC`). WMI is not a single program but consists of several programs and various databases, also known as repositories.
## Footprinting the Service

The initialization of the WMI communication always takes place on `TCP` port `135`, and after the successful establishment of the connection, the communication is moved to a random port. For example, the program [wmiexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py) from the Impacket toolkit can be used for this.

#### WMIexec.py

Windows Remote Management Protocols

``` bash
astrocat@htb[/htb]$ /usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] SMBv3.0 dialect used
ILF-SQL-01
```
