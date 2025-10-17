SNMP primarily uses two main UDP ports:
1. UDP Port 161 for the SNMP agent.
2. UDP Port 162 for the SNMP manager.

#### MIB

To ensure that SNMP access works across manufacturers and with different client-server combinations, the `Management Information Base` (`MIB`) was created. MIB is an independent format for storing device information. A MIB is a text file in which all queryable SNMP objects of a device are listed in a standardized tree hierarchy. It contains at least one `Object Identifier` (`OID`), which, in addition to the necessary unique address and a name, also provides information about the type, access rights, and a description of the respective object. MIB files are written in the `Abstract Syntax Notation One` (`ASN.1`) based ASCII text format. The MIBs do not contain data, but they explain where to find which information and what it looks like, which returns values for the specific OID, or which data type is used.
#### OID

An OID represents a node in a hierarchical namespace. A sequence of numbers uniquely identifies each node, allowing the node's position in the tree to be determined. The longer the chain, the more specific the information. Many nodes in the OID tree contain nothing except references to those below them. The OIDs consist of integers and are usually concatenated by dot notation. We can look up many MIBs for the associated OIDs in the [Object Identifier Registry](https://www.alvestrand.no/objectid/).

#### Footprinting the Service

For footprinting SNMP, we can use tools like snmpwalk, onesixtyone, and braa. Snmpwalk is used to query the OIDs with their information. Onesixtyone can be used to brute-force the names of the community strings since they can be named arbitrarily by the administrator. Since these community strings can be bound to any source, identifying the existing community strings can take quite some time.

|Feature|	SNMPv1	|SNMPv2c	|SNMPv3|
|-|-|-|-|
|Authentication|	None|	Community-based (Plain Text)|	Username & Password|
|Encryption|	None (Plain Text)	|None (Plain Text)	|Supported (Pre-Shared Key)|
##### SNMPwalk

``` bash
astrocat@htb[/htb]$ snmpwalk -v2c -c public 10.129.14.128

iso.3.6.1.2.1.1.1.0 = STRING: "Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
iso.3.6.1.2.1.1.3.0 = Timeticks: (5134) 0:00:51.34
iso.3.6.1.2.1.1.4.0 = STRING: "mrb3n@inlanefreight.htb"
iso.3.6.1.2.1.1.5.0 = STRING: "htb"
iso.3.6.1.2.1.1.6.0 = STRING: "Sitting on the Dock of the Bay"
```

##### OneSixtyOne

``` bash
astrocat@htb[/htb]$ sudo apt install onesixtyone
astrocat@htb[/htb]$ onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128

Scanning 1 hosts, 3220 communities
10.129.14.128 [public] Linux htb 5.11.0-37-generic #41~20.04.2-Ubuntu SMP Fri Sep 24 09:06:38 UTC 2021 x86_64
```

We can use the tool [crunch](https://secf00tprint.github.io/blog/passwords/crunch/advanced/en) to create custom wordlists. Creating custom wordlists is not an essential part of this module, but more details can be found in the module [Cracking Passwords With Hashcat](https://academy.hackthebox.com/course/preview/cracking-passwords-with-hashcat).

Once we know a community string, we can use it with [braa](https://github.com/mteg/braa) to brute-force the individual OIDs and enumerate the information behind them.
##### Braa
 
 ``` bash
astrocat@htb[/htb]$ sudo apt install braa
astrocat@htb[/htb]$ braa <community string>@<IP>:.1.3.6.*   # Syntax
astrocat@htb[/htb]$ braa public@10.129.14.128:.1.3.6.*

10.129.14.128:20ms:.1.3.6.1.2.1.1.1.0:Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64
10.129.14.128:20ms:.1.3.6.1.2.1.1.2.0:.1.3.6.1.4.1.8072.3.2.10
10.129.14.128:20ms:.1.3.6.1.2.1.1.3.0:548
10.129.14.128:20ms:.1.3.6.1.2.1.1.4.0:mrb3n@inlanefreight.htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.5.0:htb
```
