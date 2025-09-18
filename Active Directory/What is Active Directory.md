![[whyad5.png]]

### Active Directory (AD) Evolution & Core Concepts

- **Foundation:** AD is built upon **LDAP** (Lightweight Directory Access Protocol) and was conceptually predated by X.500 directories like Novell Directory Services (1993).
- **Official Debut:** First appeared with **Windows Server 2000**.
- **Core Protocols:** Integrates standard protocols like **LDAP** and **Kerberos** with Microsoft's own proprietary elements.
- **Forests (Server 2003):** This update introduced the **Forest** concept, a top-level container for domains, users, computers, and other objects. This is a critical concept for understanding trust relationships and attack paths.
- **Federation & SSO (Server 2008):** **Active Directory Federation Services (ADFS)** was introduced, enabling Single Sign-On (SSO). It uses a **claims-based access control** model, where an identity provider issues a security token with claims about a user's identity. This is a key area to probe for misconfigurations.
- **Cloud Integration (Server 2016):** Saw a major push towards the cloud with **Azure AD Connect**, used for syncing on-premise AD with Office 365/Azure AD. This hybrid setup introduces new potential vulnerabilities.
- **Security Enhancements (Server 2016):** **Group Managed Service Accounts (gMSA)** were introduced. These are important from a pentesting perspective as they are designed to be more secure than regular service accounts and are a recommended defense against **Kerberoasting attacks**. Finding legacy service accounts instead of gMSAs can be a significant finding.
### AD Attacks & Tools Timeline

- **2021:**
    
    - **PrintNightmare:** RCE vulnerability in the Windows Print Spooler.
        
    - **Shadow Credentials:** Privilege escalation attack allowing impersonation of users/computers.
        
    - **noPac:** Attack that can lead to full domain compromise from a standard user account.
        
- **2020:**
    
    - **ZeroLogon:** Critical flaw allowing an attacker to impersonate any unpatched Domain Controller.
        
- **2019:**
    
    - **Kerberoasting Revisited:** New approaches to Kerberoasting presented by harmj0y.
        
    - **Resource-Based Constrained Delegation (RBCD) Abuse:** Techniques published by Elad Shamir.
        
    - **Empire 3.0:** Python 3 rewrite of the PowerShell Empire framework.
        
- **2018:**
    
    - **Printer Bug / SpoolSample:** Coerces Windows hosts to authenticate to other machines.
        
    - **Rubeus:** Toolkit released by harmj0y for attacking Kerberos.
        
    - **Forest Trust Attacks:** Research published by harmj0y on attacking across forest trusts.
        
    - **DCShadow:** Attack technique for creating rogue Domain Controllers.
        
    - **Ping Castle:** AD security audit tool released.
        
- **2017:**
    
    - **ASREPRoast:** Technique for attacking user accounts that don't require Kerberos pre-authentication.
        
    - **ACL Attacks ("ACE Up the Sleeve"):** Pivotal research on abusing Access Control Lists in AD.
        
    - **Domain Trust Attacks:** harmj0y released a guide on enumerating and attacking domain trusts.
        
- **2016:**
    
    - **BloodHound:** Game-changing tool released for visualizing attack paths in AD.
        
- **2015:**
    
    - **PowerShell Empire:** C2 framework released.
        
    - **PowerView 2.0:** Powerful AD reconnaissance tool.
        
    - **DCSync:** Attack added to `mimikatz` to simulate DC replication and steal credentials.
        
    - **CrackMapExec (CME):** Initial stable release of this popular network enumeration tool.
        
    - **Unconstrained Delegation:** Research highlighting its dangers.
        
    - **Impacket:** A key collection of Python tools for network protocol attacks.
        
- **2014:**
    
    - **PowerView:** The first version of the AD reconnaissance tool was released.
        
    - **Kerberoasting:** Attack first presented by Tim Medin.
        
- **2013:**
    
    - **Responder:** Tool released for LLMNR/NBT-NS/MDNS poisoning to capture hashes and perform SMB Relay attacks.