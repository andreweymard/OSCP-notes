[Recommended events to monitor](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor)
[Database of Event IDs](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/)
### What is the Windows Event Log?
Simply put, an `event` is any action or occurrence that can be identified and classified by a system's hardware or software. `Events` can be generated or triggered through a variety of different ways including some of the following:

- User-Generated Events
    - Movement of a mouse, typing on a keyboard, other user-controlled peripherals, etc.
- Application Generated Events
    - Application updates, crashes, memory usage/consumption, etc.
- System Generated Events
    - System uptime, system updates, driver loading/unloading, user login, etc.

* [Event Logging](https://learn.microsoft.com/en-us/windows/win32/eventlog/event-logging) as defined by Microsoft:
"`...provides a standard, centralized way for applications (and the operating system) to record important software and hardware events.`"

## Event Log Categories and Types

The main four log categories include application, security, setup, and system. Another type of category also exists called `forwarded events`.

|Log Category|Log Description|
|---|---|
|System Log|The system log contains events related to the Windows system and its components. A system-level event could be a service failing at startup.|
|Security Log|Self-explanatory; these include security-related events such as failed and successful logins, and file creation/deletion. These can be used to detect various types of attacks that we will cover in later modules.|
|Application Log|This stores events related to any software/application installed on the system. For example, if Slack has trouble starting it will be recorded in this log.|
|Setup Log|This log holds any events that are generated when the Windows operating system is installed. In a domain environment, events related to Active Directory will be recorded in this log on domain controller hosts.|
|Forwarded Events|Logs that are forwarded from other hosts within the same network.|

## Event Types

There are five types of events that can be logged on Windows systems:

|Type of Event|Event Description|
|---|---|
|Error|Indicates a major problem, such as a service failing to load during startup, has occurred.|
|Warning|A less significant log but one that may indicate a possible problem in the future. One example is low disk space. A Warning event will be logged to note that a problem may occur down the road. A Warning event is typically when an application can recover from the event without losing functionality or data.|
|Information|Recorded upon the successful operation of an application, driver, or service, such as when a network driver loads successfully. Typically not every desktop application will log an event each time they start, as this could lead to a considerable amount of extra "noise" in the logs.|
|Success Audit|Recorded when an audited security access attempt is successful, such as when a user logs on to a system.|
|Failure Audit|Recorded when an audited security access attempt fails, such as when a user attempts to log in but types their password in wrong. Many audit failure events could indicate an attack, such as Password Spraying.|

## Event Severity Levels

Each log can have one of five severity levels associated with it, denoted by a number:

|Severity Level|Level #|Description|
|---|---|---|
|Verbose|5|Progress or success messages.|
|Information|4|An event that occurred on the system but did not cause any issues.|
|Warning|3|A potential problem that a sysadmin should dig into.|
|Error|2|An issue related to the system or service that does not require immediate attention.|
|Critical|1|This indicates a significant issue related to an application or a system that requires urgent attention by a sysadmin that, if not addressed, could lead to system or application instability.|

## Elements of a Windows Event Log

The Windows Event Log provides information about hardware and software events on a Windows system. All event logs are stored in a standard format and include the following elements:

- `Log name`: As discussed above, the name of the event log where the events will be written. By default, events are logged for `system`, `security`, and `applications`.
- `Event date/time`: Date and time when the event occurred
- `Task Category`: The type of recorded event log
- `Event ID`: A unique identifier for sysadmins to identify a specific logged event
- `Source`: Where the log originated from, typically the name of a program or software application
- `Level`: Severity level of the event. This can be information, error, verbose, warning, critical
- `User`: Username of who logged onto the host when the event occurred
- `Computer`: Name of the computer where the event is logged

By default, Windows Event Logs are stored in `C:\Windows\System32\winevt\logs` with the file extension `.evtx`.

### wevtutil
``` PowerShell
PS C:\Users\andre> wevtutil /?
Windows Events Command Line Utility.

Enables you to retrieve information about event logs and publishers, install
and uninstall event manifests, run queries, and export, archive, and clear logs.

Usage:

You can use either the short (for example, ep /uni) or long (for example,
enum-publishers /unicode) version of the command and option names. Commands,
options and option values are not case-sensitive.

Variables are noted in all upper-case.

wevtutil COMMAND [ARGUMENT [ARGUMENT] ...] [/OPTION:VALUE [/OPTION:VALUE] ...]

Commands:

el | enum-logs          List log names.
gl | get-log            Get log configuration information.
sl | set-log            Modify configuration of a log.
ep | enum-publishers    List event publishers.
gp | get-publisher      Get publisher configuration information.
im | install-manifest   Install event publishers and logs from manifest.
um | uninstall-manifest Uninstall event publishers and logs from manifest.
qe | query-events       Query events from a log or log file.
gli | get-log-info      Get log status information.
epl | export-log        Export a log.
al | archive-log        Archive an exported log.
cl | clear-log          Clear a log.

Common options:

/{r | remote}:VALUE
If specified, run the command on a remote computer. VALUE is the remote computer
name. Options /im and /um do not support remote operations.

/{u | username}:VALUE
Specify a different user to log on to the remote computer. VALUE is a user name
in the form domain\user or user. Only applicable when option /r is specified.

/{p | password}:VALUE
Password for the specified user. If not specified, or if VALUE is "*", the user
will be prompted to enter a password. Only applicable when the /u option is
specified.

/{a | authentication}:[Default|Negotiate|Kerberos|NTLM]
Authentication type for connecting to remote computer. The default is Negotiate.

/{uni | unicode}:[true|false]
Display output in Unicode. If true, then output is in Unicode.

To learn more about a specific command, type the following:

wevtutil COMMAND /?
```

Enumerating Log Sources - wevtutil el
Gathering Log Information - wevtutil gl "Windows PowerShell"
The gli parameter will give us specific status information about the log or log file, such as the creation time, last access and write times, file size, number of log records, and more.
Querying Events - wevtutil qe Security /c:5 /rd:true /f:text
Exporting Events - wevtutil epl System C:\system_export.evtx
PowerShell - Listing All Logs - Get-WinEvent -ListLog *
Security Log Details - Get-WinEvent -ListLog Security
Querying Last Five Events - `Get-WinEvent -LogName 'Security' -MaxEvents 5 | Select-Object -ExpandProperty Message`
Filtering for Logon Failures - Get-WinEvent -FilterHashTable @{LogName='Security';ID='4625 '}
System logs for only critical events with information level - `Get-WinEvent -FilterHashTable @{LogName='System';Level='1'} | select-object -ExpandProperty Message`
