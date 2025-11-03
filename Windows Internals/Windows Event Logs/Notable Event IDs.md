## ## 👾 Sysmon

Sysmon (System Monitor) provides deep, kernel-level logging that is essential for threat hunting. You must install and configure it separately.

- **Event ID 1: Process Creation**
    
    - **Why it matters:** This is the most critical Sysmon event. It tells you _what_ process started, _who_ started it, its command line, and the parent process. It's the starting point for tracing almost any malicious activity.
        
- **Event ID 3: Network Connection**
    
    - **Why it matters:** Logs all TCP and UDP connections made by a process. This is how you spot a tool connecting to a command and control (C2) server or moving laterally.
        
- **Event ID 7: Image Loaded**
    
    - **Why it matters:** Tracks when a process loads a DLL (or other image file). This is how you detected the DLL hijacking in your previous question. It's also used to find unmanaged code injection, as a non-.NET process loading `clr.dll` or `mscoree.dll` is highly suspicious.
        
- **Event ID 10: Process Access**
    
    - **Why it matters:** Logs when one process attempts to access another process's memory. This is the classic indicator for credential-dumping tools like Mimikatz, which read the memory of the `lsass.exe` process.
        
- **Event ID 22: DNS Query**
    
    - **Why it matters:** Shows all DNS requests made by a process. This is invaluable for identifying C2 communications, as malware often queries for a "beacon" domain.
        

---

## ## 📜 AMSI (Antimalware Scan Interface)

AMSI doesn't have its own dedicated event log. Instead, it's an interface that **feeds its findings** into other logs. When AMSI catches something, you'll find the alert in one of these two places:

- **PowerShell Logs (Event ID 4103 & 4104)**
    
    - **Log Name:** `Microsoft-Windows-PowerShell/Operational`
        
    - **Why they matter:** **Event ID 4103** (Module Logging) and **Event ID 4104** (Script Block Logging) capture the full content of PowerShell commands and scripts. When a script is blocked _by AMSI_, the block event and the malicious script content are often logged here. This is your primary source for seeing _what_ malicious script was caught.
        
- **Windows Defender (Event ID 1116 & 1117)**
    
    - **Log Name:** `Microsoft-Windows-Windows Defender/Operational`
        
    - **Why they matter:** **Event ID 1116** logs a "Malware detection," and **Event ID 1117** shows the "remediation action" taken. When AMSI scans a script (from PowerShell, VBScript, etc.) and determines it's malicious, it tells Windows Defender, which then generates these events. These logs confirm the detection and what was done about it.
        

---

## ## 🖥️ Windows Event Viewer (Native Logs)

These are the most important logs that are available on Windows by default (though some require advanced audit policies to be enabled).

### 1. Logon/Logoff Events

- **Event ID 4624: An account was successfully logged on.**
    
    - **Why it matters:** Tracks successful logins. You look for _anomalies_, like a user logging in at 3 AM, an administrator logging into a workstation (not a server), or a login from an unusual source.
        
- **Event ID 4625: An account failed to log on.**
    
    - **Why it matters:** This is your brute-force and password-spraying detector. A high volume of failed logins from a single source is a major red flag.
        
- **Event ID 4648: A logon was attempted using explicit credentials.**
    
    - **Why it matters:** This event is created when a user uses the "Run as" feature or when a tool (like `net use` or `psexec`) is used for lateral movement. It's a key indicator of an attacker moving through the network.
        

### 2. Process & Service Events

- **Event ID 4688: A new process has been created.**
    
    - **Why it matters:** This is the native (and less detailed) version of Sysmon ID 1. If you don't have Sysmon, this is the next best thing. You must enable "Audit Process Creation" to see it.
        
- **Event ID 7045: A new service was installed.**
    
    - **Why it matters:** Attackers install services for persistence. A new service, especially with a suspicious name or path (like `svchost.exe` from a temp folder), is a critical alert. Found in the `System` log.
        

### 3. Log & Policy Management

- **Event ID 1102: The audit log was cleared.**
    
    - **Why it matters:** This is the #1 "attacker is covering their tracks" event. No legitimate administrator should be clearing the Security log. This event is an "all hands on deck" alert.
        
- **Event ID 4719: System audit policy was changed.**
    
    - **Why it matters:** This shows an attacker _disabling_ the logging you rely on. It's a clear sign of an advanced attacker attempting to become invisible.