The Windows Event Log provides information about hardware and software events on a Windows system. All event logs are stored in a standard format and include the following elements:

- `Log name`: As discussed above, the name of the event log where the events will be written. By default, events are logged for `system`, `security`, and `applications`.
- `Event date/time`: Date and time when the event occurred
- `Task Category`: The type of recorded event log
- `Event ID`: A unique identifier for sysadmins to identify a specific logged event
- `Source`: Where the log originated from, typically the name of a program or software application
- `Level`: Severity level of the event. This can be information, error, verbose, warning, critical
- `Keywords`: Keywords are flags that allow us to categorize events in ways beyond the other classification options. These are generally broad categories, such as "Audit Success" or "Audit Failure" in the Security log.
- `User`: Username of who logged onto the host when the event occurred
- `Computer`: Name of the computer where the event is logged
- `OpCode`: This field can identify the specific operation that the event reports.
- `Logged`: The date and time when the event was logged.
- `XML Data`: All the above information is also included in an XML format along with additional event data.


![[Pasted image 20251102092930.png]]
```shell-session
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">
*[EventData[Data[@Name='ProcessName']='C:\Windows\WinSxS\amd64_microsoft-windows-servicingstack_31bf3856ad364e35_10.0.19041.1790_none_7df2aec07ca10e81\TiWorker.exe']] and *[EventData[Data[@Name='ObjectName']='C:\Windows\Microsoft.NET\Framework64\v4.0.30319\WPF\wpfgfx_v0400.dll']]
</Select>
  </Query>
</QueryList>
```
