CIFS is considered a specific version of the SMB protocol, primarily aligning with SMB version 1. When SMB commands are transmitted over Samba to an older NetBIOS service, connections typically occur over TCP ports 137, 138, and 139. In contrast, CIFS operates over TCP port 445 exclusively. There are several versions of SMB, including newer versions like SMB 2 and SMB 3, which offer improvements and are preferred in modern infrastructures, while older versions like SMB 1 (CIFS) are considered outdated but may still be used in specific environments.

| SMB Version | Supported                           | Features                                                               |
| ----------- | ----------------------------------- | ---------------------------------------------------------------------- |
| CIFS        | Windows NT 4.0                      | Communication via NetBIOS interface                                    |
| SMB 1.0     | Windows 2000                        | Direct connection via TCP                                              |
| SMB 2.0     | Windows Vista, Windows Server 2008  | Performance upgrades, improved message signing, caching feature        |
| SMB 2.1     | Windows 7, Windows Server 2008 R2   | Locking mechanisms                                                     |
| SMB 3.0     | Windows 8, Windows Server 2012      | Multichannel connections, end-to-end encryption, remote storage access |
| SMB 3.0.2   | Windows 8.1, Windows Server 2012 R2 |                                                                        |
| SMB 3.1.1   | Windows 10, Windows Server 2016     | Integrity checking, AES-128 encryption                                 |

We know that Samba is suitable for both Linux and Windows systems. In a network, each host participates in the same `workgroup`. A workgroup is a group name that identifies an arbitrary collection of computers and their resources on an SMB network. There can be multiple workgroups on the network at any given time. IBM developed an `application programming interface` (`API`) for networking computers called the `Network Basic Input/Output System` (`NetBIOS`). The NetBIOS API provided a blueprint for an application to connect and share data with other computers. In a NetBIOS environment, when a machine goes online, it needs a name, which is done through the so-called `name registration` procedure. Either each host reserves its hostname on the network, or the [NetBIOS Name Server](https://networkencyclopedia.com/netbios-name-server-nbns/) (`NBNS`) is used for this purpose. It also has been enhanced to [Windows Internet Name Service](https://networkencyclopedia.com/windows-internet-name-service-wins/) (`WINS`).

### Default Configuration

As we can imagine, Samba offers a wide range of settings that we can configure. Again, we define the settings via a text file where we can get an overview of some of the settings. These settings look like the following when filtered out:
Default Configuration
SMB

``` bash
astrocat@htb[/htb]$ cat /etc/samba/smb.conf | grep -v "#\|\;" 

[global]
   workgroup = DEV.INFREIGHT.HTB
   server string = DEVSMB
   log file = /var/log/samba/log.%m
   max log size = 1000
   logging = file
   panic action = /usr/share/samba/panic-action %d

   server role = standalone server
   obey pam restrictions = yes
   unix password sync = yes

   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .

   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes

[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700

[print$]
   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no
```

We see global settings and two shares that are intended for printers. The global settings are the configuration of the available SMB server that is used for all shares. In the individual shares, however, the global settings can be overwritten, which can be configured with high probability even incorrectly. Let us look at some of the settings to understand how the shares are configured in Samba.

| Setting |	Description |
|------------|-----------------|
| [sharename] |	The name of the network share. |
| workgroup = WORKGROUP/DOMAIN |	Workgroup that will appear when clients query. |
| path = /path/here/ 	|The directory to which user is to be given access. |
|server string = STRING |	The string that will show up when a connection is initiated.|
|unix password sync = yes |	Synchronize the UNIX password with the SMB password?|
|usershare allow guests = yes |	Allow non-authenticated users to access defined share?|
|map to guest = bad user |	What to do when a user login request doesn't match a valid UNIX user?|
|browseable = yes |	Should this share be shown in the list of available shares?|
|guest ok = yes |	Allow connecting to the service without using a password?|
|read only = yes |	Allow users to read files only?|
|create mask = 0700 |	What permissions need to be set for newly created files?
### Dangerous Settings 

Some of the above settings already bring some sensitive options. However, suppose we question the settings listed below and ask ourselves what the employees could gain from them, as well as attackers. In that case, we will see what advantages and disadvantages the settings bring with them. Let us take the setting browseable = yes as an example. If we as administrators adopt this setting, the company's employees will have the comfort of being able to look at the individual folders with the contents. Many folders are eventually used for better organization and structure. If the employee can browse through the shares, the attacker will also be able to do so after successful access.

|Setting |	Description|
|-|-|
|browseable = yes | Allow listing available shares in the current share?|
|read only = no |	Forbid the creation and modification of files?|
|writable = yes |	Allow users to create and modify files?|
|guest ok = yes |	Allow connecting to the service without using a password?|
|enable privileges = yes |	Honor privileges assigned to specific SID?|
|create mask = 0777 |	What permissions must be assigned to the newly created files?|
|directory mask = 0777 |	What permissions must be assigned to the newly created directories?|
|logon script = script.sh |	What script needs to be executed on the user's login?|
|magic script = script.sh |	Which script should be executed when the script gets closed?|
|magic output = script.out |	Where the output of the magic script needs to be stored?|

### RPCclient

The rpcclient offers us many different requests with which we can execute specific functions on the SMB server to get information. A complete list of all these functions can be found on the man page of the rpcclient.

| Query                     | Description                                                       |
| ------------------------- | ----------------------------------------------------------------- |
| srvinfo                   | Server information                                                |
| enumdomains               | Enumerate all domains that are deployed in the network.           |
| querydominfo              | Provides domain, server, and user information of deployed domains |
| netshareenumall           | Enumerates all available shares.                                  |
| netsharegetinfo \<share\> | Provides information about a specific share.                      |
| enumdomusers              | Enumerates all domain users.                                      |
| queryuser \<RID\>         | Provides information about a specific user.                       |
#### SMBclient - Connecting to the Share

``` bash
astrocat@htb[/htb]$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled -- no workgroup available
```
