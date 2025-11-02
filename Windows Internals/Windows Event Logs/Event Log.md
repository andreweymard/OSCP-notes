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

By default, Windows <mark>Event Logs are stored</mark> in `C:\Windows\System32\winevt\logs` with the file extension `.evtx`.
## Event Log Categories and Types

The main four log categories include application, security, setup, and system. Another type of category also exists called `forwarded events`.

| Log Category     | Log Description                                                                                                                                                                                                     |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| System Log       | The system log contains events related to the Windows system and its components. A system-level event could be a service failing at startup.                                                                        |
| Security Log     | Self-explanatory; these include security-related events such as failed and successful logins, and file creation/deletion. These can be used to detect various types of attacks that we will cover in later modules. |
| Application Log  | This stores events related to any software/application installed on the system. For example, if Slack has trouble starting it will be recorded in this log.                                                         |
| Setup Log        | This log holds any events that are generated when the Windows operating system is installed. In a domain environment, events related to Active Directory will be recorded in this log on domain controller hosts.   |
| Forwarded Events | Logs that are forwarded from other hosts within the same network.                                                                                                                                                   |

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
