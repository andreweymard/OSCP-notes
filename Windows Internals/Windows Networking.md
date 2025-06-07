It would be worthwhile to look at this article by Microsoft on [Test-NetConnection](https://learn.microsoft.com/en-us/powershell/module/nettcpip/test-netconnection?view=windowsserver2022-ps).

| **Protocol** | **Description**                                                                                                                                                                                                                                                                                                                                                                    |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SMB`        | [SMB](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/4287490c-602c-41c0-a23e-140a1f137832) provides Windows hosts with the capability to share resources, files, and a standard way of authenticating between hosts to determine if access to resources is allowed. For other distros, SAMBA is the open-source option.                                     |
| `Netbios`    | [NetBios](https://www.ietf.org/rfc/rfc1001.txt) itself isn't directly a service or protocol but a connection and conversation mechanism widely used in networks. It was the original transport mechanism for SMB, but that has since changed. Now it serves as an alternate identification mechanism when DNS fails. Can also be known as NBT-NS (NetBIOS name service).           |
| `LDAP`       | [LDAP](https://www.rfc-editor.org/rfc/rfc4511) is an `open-source` cross-platform protocol used for `authentication` and `authorization` with various directory services. This is how many different devices in modern networks can communicate with large directory structure services such as `Active Directory`.                                                                |
| `LLMNR`      | [LLMNR](https://www.rfc-editor.org/rfc/rfc4795) provides a name resolution service based on DNS and works if DNS is not available or functioning. This protocol is a multicast protocol and, as such, works only on local links ( within a normal broadcast domain, not across layer three links).                                                                                 |
| `DNS`        | [DNS](https://datatracker.ietf.org/doc/html/rfc1034) is a common naming standard used across the Internet and in most modern network types. DNS allows us to reference hosts by a unique name instead of their IP address. This is how we can reference a website by "WWW.google.com" instead of "8.8.8.8". Internally this is how we request resources and access from a network. |
| `HTTP/HTTPS` | [HTTP/S](https://www.rfc-editor.org/rfc/rfc2818) HTTP and HTTPS are the insecure and secure way we request and utilize resources over the Internet. These protocols are used to access and utilize resources such as web servers, send and receive data from remote sources, and much more.                                                                                        |
| `Kerberos`   | [Kerberos](https://web.mit.edu/kerberos/) is a network level authentication protocol. In modern times, we are most likely to see it when dealing with Active Directory authentication when clients request tickets for authorization to use domain resources.                                                                                                                      |
| `WinRM`      | [WinRM](https://learn.microsoft.com/en-us/windows/win32/winrm/portal) Is an implementation of the WS-Management protocol. It can be used to manage the hardware and software functionalities of hosts. It is mainly used in IT administration but can also be used for host enumeration and as a scripting engine.                                                                 |
| `RDP`        | [RDP](https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/rds-plan-access-from-anywhere) is a Windows implementation of a network UI services protocol that provides users with a Graphical interface to access hosts over a network connection. This allows for full UI use to include the passing of keyboard and mouse input to the remote host.    |
| `SSH`        | [SSH](https://datatracker.ietf.org/doc/html/rfc4251) is a secure protocol that can be used for secure host access, transfer of files, and general communication between network hosts. It provides a way to securely access hosts and services over insecure networks.                                                                                                             |
`ipconfig /all`
`arp -a`
`netstat -an`

#### [Net Cmdlets](obsidian://open?vault=HTB-OSCP&file=Windows%20Internals%2FInteracting%20with%20Windows%2FPowershell)

| **Cmdlet**           | **Description**                                                                                           |
| -------------------- | --------------------------------------------------------------------------------------------------------- |
| `Get-NetIPInterface` | Retrieve all `visible` network adapter `properties`.                                                      |
| `Get-NetIPAddress`   | Retrieves the `IP configurations` of each adapter. Similar to `IPConfig`.                                 |
| `Get-NetNeighbor`    | Retrieves the `neighbor entries` from the cache. Similar to `arp -a`.                                     |
| `Get-Netroute`       | Will print the current `route table`. Similar to `IPRoute`.                                               |
| `Set-NetAdapter`     | Set basic adapter properties at the `Layer-2` level such as VLAN id, description, and MAC-Address.        |
| `Set-NetIPInterface` | Modifies the `settings` of an `interface` to include DHCP status, MTU, and other metrics.                 |
| `New-NetIPAddress`   | Creates and configures an `IP address`.                                                                   |
| `Set-NetIPAddress`   | Modifies the `configuration` of a network adapter.                                                        |
| `Disable-NetAdapter` | Used to `disable` network adapter interfaces.                                                             |
| `Enable-NetAdapter`  | Used to turn network adapters back on and `allow` network connections.                                    |
| `Restart-NetAdapter` | Used to restart an adapter. It can be useful to help push `changes` made to adapter `settings`.           |
| `test-NetConnection` | Allows for `diagnostic` checks to be ran on a connection. It supports ping, tcp, route tracing, and more. |
`Get-NetIPInterface`
`Get-NetIPAddress -ifIndex 25`
`Set-NetIPInterface -InterfaceIndex 25 -Dhcp Disabled`
```powershell
PS C:\htb> Set-NetIPAddress -InterfaceIndex 25 -IPAddress 10.10.100.54 -PrefixLength 24

PS C:\htb> Get-NetIPAddress -ifindex 20 | ft InterfaceIndex,InterfaceAlias,IPAddress,PrefixLength

InterfaceIndex InterfaceAlias IPAddress                   PrefixLength
-------------- -------------- ---------                   ------------
            20 Ethernet 3     fe80::7408:bbf:954a:6ae5%20           64
            20 Ethernet 3     10.10.100.54                          24

PS C:\htb> Get-NetIPinterface -ifindex 20 | ft ifIndex,InterfaceAlias,Dhcp

ifIndex InterfaceAlias     Dhcp
------- --------------     ----
     20 Ethernet 3     Disabled
     20 Ethernet 3     Disabled
```
`Restart-NetAdapter -Name 'Ethernet 3'`
`Test-NetConnection`
`Get-WindowsCapability -Online | Where-Object State -ne "NotPresent"`
`Set-Service -Name sshd -StartupType 'Automatic'`

#### WinRM
[Windows Remote Management (WinRM)](https://docs.microsoft.com/en-us/windows/win32/winrm/portal) can be configured using dedicated PowerShell cmdlets and we can enter into a PowerShell interactive session as well as issue commands on remote Windows target(s). We will notice that WinRM is more commonly enabled on Windows Server operating systems, so IT admins can perform tasks on one or multiple hosts. It's enabled by default in Windows Server.
When WinRM is enabled on a Windows target, it listens on logical ports `5985` & `5986`.

```powershell
PS C:\WINDOWS\system32> winrm quickconfig

WinRM service is already running on this machine.
WinRM is not set up to allow remote access to this machine for management.
The following changes must be made:

Enable the WinRM firewall exception.
Configure LocalAccountTokenFilterPolicy to grant administrative rights remotely to local users.

Make these changes [y/n]? y

WinRM has been updated for remote management.

WinRM firewall exception enabled.
Configured LocalAccountTokenFilterPolicy to grant administrative rights remotely to local users.
```
IT admins should take further steps to harden these WinRM configurations, especially if the system will be remotely accessible over the Internet. Among some of these hardening options are:

- Configure TrustedHosts to include just IP addresses/hostnames that will be used for remote management
- Configure HTTPS for transport
- Join Windows systems to an Active Directory Domain Environment and Enforce Kerberos Authentication

`Test-WSMan -ComputerName "10.129.224.248" -Authentication Negotiate`

### What If We Can't Use Invoke-WebRequest?

So what happens if we are restricted from using `Invoke-WebRequest` for some reason? Not to fear, Windows provides several different methods to interact with web clients. The first and more challenging interaction path is utilizing the [.Net.WebClient](https://learn.microsoft.com/en-us/dotnet/api/system.net.webclient?view=net-7.0) class. This handy class is a .Net call we can utilize as Windows uses and understands .Net. This class contains standard system.net methods for interacting with resources via a URI (web addresses like github.com/project/tool.ps1). Let's look at an example:

#### Net.WebClient Download

Interacting With The Web

```powershell-session
PS C:\htb> (New-Object Net.WebClient).DownloadFile("https://github.com/BloodHoundAD/BloodHound/releases/download/4.2.0/BloodHound-win32-x64.zip", "Bloodhound.zip")

PS C:\htb> ls

    Directory: C:\Users\MTanaka

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          11/10/2022 10:45 AM      108511752 Bloodhound.zip
-a---           6/14/2022  8:22 AM           4418 passwords.kdbx
-a---            9/9/2020  4:54 PM         696576 Start.exe
-a---           9/11/2021 12:58 PM              0 sticky.gpr
-a---          11/10/2022 10:44 AM      108511752 test.zip
```

So it worked. Let's break down what we did:

- First we have the Download cradle `(New-Object Net.WebClient).DownloadFile()`, which is how we tell it to execute our request.
- Next, we need to include the URI of the file we want to download as the first parameter in the (). For this example, that was `"https://github.com/BloodHoundAD/BloodHound/releases/download/4.2.0/BloodHound-win32-x64.zip"`.
- Finally, we need to tell the command where we want the file written to with the second parameter `, "BloodHound.zip"`.