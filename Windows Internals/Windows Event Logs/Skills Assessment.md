### Question 1

#### "By examining the logs located in the "C:\Logs\DLLHijack" directory, determine the process responsible for executing a DLL hijacking attack. Enter the process name as your answer. Answer format: _.exe"

Students need to connect to the spawned target using RDP, utilizing the credentials `Administrator:HTB_@cad3my_lab_W1n10_r00t!@0`:

Code: shell

```shell
xfreerdp /v:STMIP /u:Administrator /p:'HTB_@cad3my_lab_W1n10_r00t!@0' /dynamic-resolution
```

Skills Assessment

```shell-session
┌─[us-academy-1]─[10.10.14.223]─[htb-ac-594497@htb-7hqbxrdnrv]─[~]
└──╼ [★]$ xfreerdp /v:10.129.205.123 /u:Administrator /p:'HTB_@cad3my_lab_W1n10_r00t!@0' /dynamic-resolution 

[02:15:41:799] [2259:2260] [INFO][com.freerdp.crypto] - creating directory /home/htb-ac-594497/.config/freerdp
[02:15:41:799] [2259:2260] [INFO][com.freerdp.crypto] - creating directory [/home/htb-ac-594497/.config/freerdp/certs]
[02:15:41:799] [2259:2260] [INFO][com.freerdp.crypto] - created directory [/home/htb-ac-594497/.config/freerdp/server]
[02:15:41:815] [2259:2260] [WARN][com.freerdp.crypto] - Certificate verification failure 'self signed certificate (18)' at stack position 0
[02:15:41:815] [2259:2260] [WARN][com.freerdp.crypto] - CN = DESKTOP-NU10MTO
```

Then, students need to analyze the `DLLHijack.evtx` event file using `PowerShell` and the `Get-WinEvent` Cmdlet. To detect a DLL hijack, students need to focus on `Event Type 7`, which corresponds to module load events. Additionally, students should search for events triggered by unsigned DLLs.

Code: powershell

```powershell
Get-WinEvent -Path 'C:\Logs\DLLHijack\DLLHijack.evtx' | Where-Object {$_.Id -eq 7} | Where-Object {$_.Properties[12].Value -like "false"} | Format-List
```

Skills Assessment

```powershell-session
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\Users\Administrator> Get-WinEvent -Path 'C:\Logs\DLLHijack\DLLHijack.evtx' | Where-Object {$_.Properties[12].Value -like "false"} | Format-List

Get-WinEvent : The maximum number of replacements has been reached
At line:1 char:1
+ Get-WinEvent -Path 'C:\Logs\DLLHijack\DLLHijack.evtx' | Where-Object  ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Get-WinEvent], EventLogException
    + FullyQualifiedErrorId : The maximum number of replacements has been reached,Microsoft.PowerShell.Commands.GetWin
   EventCommand


<SNIP>

TimeCreated  : 4/27/2022 6:39:11 PM
ProviderName : Microsoft-Windows-Sysmon
Id           : 7
Message      : Image loaded:
               RuleName: -
               UtcTime: 2022-04-28 01:39:11.859
               ProcessGuid: {67e39d39-f03f-6269-9b01-000000000300}
               ProcessId: 6868
               Image: C:\ProgramData\Dism.exe
               ImageLoaded: C:\ProgramData\DismCore.dll
               FileVersion: 0.0.0.0
               Description: FILEDESCRIPTIONGOESHERE
               Product: PRODUCTNAMEGOESHERE
               Company: -
               OriginalFileName: ORIGINALFILENAMEGOESHERE
               Hashes: SHA1=524945EE2CC863CDB57C7CCCD89607B9CD6E0524,MD5=9B5056E10FCF5959F70637553E5C1577,SHA256=6AB9D9
               4E6888FB808E7FBBE93F8F60A0D7A021D6080923A1D8596C3C8CD6B7F7,IMPHASH=5393B78894398013B4127419F1A93894
               Signed: false
               Signature: -
               SignatureStatus: Unavailable
               User: DESKTOP-R4PEEIF\waldo
```

By filtering the logs specifically for `unsigned DLL files` being loaded, it is revealed that `Dism.exe` was responsible for the `DLL hijacking attack`. This was accomplished by using `.Properties[12]`, and students can look at `Event ID 7` in `Event Viewer` to better understand the object structure:

![Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_29.png](https://academy.hackthebox.com/storage/walkthroughs/106/Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_29.png)

![Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_30.png](https://academy.hackthebox.com/storage/walkthroughs/106/Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_30.png)

Answer: {hidden}

### Question 2

#### "By examining the logs located in the "C:\Logs\PowershellExec" directory, determine the process that executed unmanaged PowerShell code. Enter the process name as your answer. Answer format: _.exe"

Using the previously established RDP session, students need to use `PowerShell` and the `Get-WinEvent` Cmdlet to analyze `PowerShellExec.evtx`. Specifically, students need to look for events where the `Image Loaded` property is set to `clr.dll`:

Code: powershell

```powershell
Get-WinEvent -Path 'C:\Logs\PowershellExec\PowershellExec.evtx' | Where-Object {$_.Properties[5].Value -like "*clr.dll*"} | Format-List
```

Skills Assessment

```powershell-session
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\Users\Administrator> Get-WinEvent -Path 'C:\Logs\PowershellExec\PowershellExec.evtx' | Where-Object {$_.Properties[5].Value -like "*clr.dll*"} | Format-List


TimeCreated  : 4/27/2022 6:59:42 PM
ProviderName : Microsoft-Windows-Sysmon
Id           : 7
Message      : Image loaded:
               RuleName: -
               UtcTime: 2022-04-28 01:59:42.194
               ProcessGuid: {67e39d39-f4cc-6269-3203-000000000300}
               ProcessId: 3776
               Image: C:\Program Files\WindowsApps\Microsoft.WindowsCalculator_10.1906.55.0_x64__8wekyb3d8bbwe\Calculator.exe
               ImageLoaded: C:\Windows\Microsoft.NET\Framework64\v4.0.30319\clr.dll
               FileVersion: 4.8.4470.0 built by: NET48REL1LAST_C
               Description: Microsoft .NET Runtime Common Language Runtime - WorkStation
               Product: Microsoft® .NET Framework
               Company: Microsoft Corporation
               OriginalFileName: clr.dll
               Hashes: SHA1=C5A99CE7425E1A2245A4C0FAC6FFD725508A6897,MD5=3C242B76E36DAB6C0B1E300AE7BC3D2E,SHA256=99ED3CC3A8CA5938783C0CAA052AC72A104FB6C7777A56D3AD7D6BBA32D52969,IMPHASH=6851068577998FF473E5933122867348
               Signed: true
               Signature: Microsoft Corporation
               SignatureStatus: Valid
               User: DESKTOP-R4PEEIF\waldo
```

Students will find `Calculator.exe` as the process responsible for executing unmanaged `PowerShell` code.

Answer: {hidden}

### Question 3

#### "By examining the logs located in the "C:\Logs\PowershellExec" directory, determine the process that injected into the process that executed unmanaged PowerShell code. Enter the process name as your answer. Answer format: _.exe"

Using the previously established RDP session, students need to use `PowerShell` and the `Get-WinEvent` Cmdlet to analyze `PowerShellExec.evtx`. Specifically, students need to look for `Event ID 8` (The `CreateRemoteThread` event detects when a process creates a thread in another process) and find events where the `TargetImage` property contains `Calculator.exe`:

Code: powershell

```powershell
Get-WinEvent -FilterHashtable @{Path='C:\Logs\PowershellExec\PowershellExec.evtx'; ID=8} | Where-Object {$_.message -like "*Calculator.exe*"} | Format-List
```

![Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_31.png](https://academy.hackthebox.com/storage/walkthroughs/106/Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_31.png)

Skills Assessment

```powershell-session
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{Path='C:\Logs\PowershellExec\PowershellExec.evtx'; ID=8} | Where-Object {$_.message -like "*Calculator.exe*"} | Format-List


TimeCreated  : 4/27/2022 7:00:13 PM
ProviderName : Microsoft-Windows-Sysmon
Id           : 8
Message      : CreateRemoteThread detected:
               RuleName: -
               UtcTime: 2022-04-28 02:00:13.593
               SourceProcessGuid: {67e39d39-f0f6-6269-b601-000000000300}
               SourceProcessId: 8364
               SourceImage: C:\Windows\System32\rundll32.exe
               TargetProcessGuid: {67e39d39-f4cc-6269-3203-000000000300}
               TargetProcessId: 3776
               TargetImage: C:\Program Files\WindowsApps\Microsoft.WindowsCalculator_10.1906.55.0_x64__8wekyb3d8bbwe\Calculator.exe
               NewThreadId: 4816
               StartAddress: 0x00000253B2180000
               StartModule: -
               StartFunction: -
               SourceUser: DESKTOP-R4PEEIF\waldo
               TargetUser: DESKTOP-R4PEEIF\waldo

TimeCreated  : 4/27/2022 6:59:42 PM
ProviderName : Microsoft-Windows-Sysmon
Id           : 8
Message      : CreateRemoteThread detected:
               RuleName: -
               UtcTime: 2022-04-28 01:59:42.176
               SourceProcessGuid: {67e39d39-f0f6-6269-b601-000000000300}
               SourceProcessId: 8364
               SourceImage: C:\Windows\System32\rundll32.exe
               TargetProcessGuid: {67e39d39-f4cc-6269-3203-000000000300}
               TargetProcessId: 3776
               TargetImage: C:\Program Files\WindowsApps\Microsoft.WindowsCalculator_10.1906.55.0_x64__8wekyb3d8bbwe\Calculator.exe
               NewThreadId: 3980
               StartAddress: 0x0000025398BD0000
               StartModule: -
               StartFunction: -
               SourceUser: DESKTOP-R4PEEIF\waldo
               TargetUser: DESKTOP-R4PEEIF\waldo

```

Students will find `rundll32.exe` injected into `Calculator.exe`.

Answer: {hidden}

### Question 4

#### "By examining the logs located in the "C:\Logs\Dump" directory, determine the process that performed an LSASS dump. Enter the process name as your answer. Answer format:_.exe"

Using the previously established RDP session, students need to use `PowerShell` and the `Get-WinEvent` Cmdlet to analyze `LsassDump.evtx`. Specifically, students need to look for event ID 10 (the Process Accessed event reports when a process opens another process) and find events where the `TargetImage` property is `lsass.exe`:

```
Get-WinEvent -FilterHashtable @{Path='C:\Logs\Dump\LsassDump.evtx'; ID=10} | Where-Object {$_.Properties[8].Value -like "*lsass*"} | Format-List
```

Skills Assessment

```powershell-session
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{Path='C:\Logs\Dump\LsassDump.evtx'; ID=10} | Where-Object {$_.Properties[8].Value -like "*lsass*"} | Format-List

<SNIP>

TimeCreated  : 4/27/2022 7:08:47 PM
ProviderName : Microsoft-Windows-Sysmon
Id           : 10
Message      : Process accessed:
               RuleName: -
               UtcTime: 2022-04-28 02:08:47.827
               SourceProcessGUID: {67e39d39-f72f-6269-6203-000000000300}
               SourceProcessId: 5560
               SourceThreadId: 3936
               SourceImage: C:\Users\waldo\Downloads\processhacker-3.0.4801-bin\64bit\ProcessHacker.exe
               TargetProcessGUID: {67e39d39-ecd9-6269-0c00-000000000300}
               TargetProcessId: 696
               TargetImage: C:\Windows\system32\lsass.exe
               GrantedAccess: 0x1400
               CallTrace: C:\Windows\SYSTEM32\ntdll.dll+9d234|C:\Users\waldo\Downloads\processhacker-3.0.4801-bin\64bit\ProcessHacker.exe+9373b|C:\Users\waldo\Downloads\processhacker-3.0.4801-bin\64bit\ProcessHacker.exe+95a1b|C:\Users\waldo\Downloads\processhacker-3.0.4
               801-bin\64bit\ProcessHacker.exe+175751|C:\Users\waldo\Downloads\processhacker-3.0.4801-bin\64bit\ProcessHacker.exe+10952b|C:\Windows\System32\KERNEL32.DLL+17034|C:\Windows\SYSTEM32\ntdll.dll+52651
               SourceUser: DESKTOP-R4PEEIF\waldo
            
```

Students will find `ProcessHacker.exe` as the process that performed the `lsass` dump.

Answer: {hidden}

### Question 5

#### "By examining the logs located in the "C:\Logs\Dump" directory, determine if an ill-intended login took place after the LSASS dump. Answer format: Yes or No"

From the previous exercise, students will know that the `lsass` dump took place on `4/27/2022 7:08:47 PM`. Therefore, students need to open `C:\Logs\Dump\SecurityLogs.evtx` in Event Viewer to compare the time stamps:

![Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_32.png](https://academy.hackthebox.com/storage/walkthroughs/106/Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_32.png)

Students will find that no additional logon events have occurred.

Answer: {hidden}

### Question 6

#### "By examining the logs located in the "C:\Logs\StrangePPID" directory, determine a process that was used to temporarily execute code based on a strange parent-child relationship. Enter the process name as your answer. Answer format: _.exe"

Students need to open the `C:\Logs\StrangePPID\StrangePPID.evtx` file in `Event Viewer` and filter for `Event ID 1`:

![Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_33.png](https://academy.hackthebox.com/storage/walkthroughs/106/Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_33.png)

Observing only four events, students will find `werfault.exe` creating a `cmd /c whoami` command.

Additionally, students can perform the analysis using `Get-WinEvent`, searching for instances of `Event ID 1` where the `Image` field contains "cmd" (the idea being to identify cases where cmd.exe is being spawned by another process):

![Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_31.png](https://academy.hackthebox.com/storage/walkthroughs/106/Windows_Event_logs_&_Finding_Evil_Walkthrough_Image_31.png)

Code: powershell

```powershell
Get-WinEvent -Path 'C:\Logs\StrangePPID\*' -FilterXPath "*[System[EventID=1]]" | where-object {$_.Properties[4].Value -like "*cmd*"} | fl
```

Skills Assessment

```powershell-session
PS C:\Users\Administrator> Get-WinEvent -Path 'C:\Logs\StrangePPID\*' -FilterXPath "*[System[EventID=1]]" | where-object {$_.Properties[4].Value -like "*cmd*"} | fl


TimeCreated  : 4/27/2022 7:18:06 PM
ProviderName : Microsoft-Windows-Sysmon
Id           : 1
Message      : Process Create:
               RuleName: -
               UtcTime: 2022-04-28 02:18:06.611
               ProcessGuid: {67e39d39-f95e-6269-8303-000000000300}
               ProcessId: 472
               Image: C:\Windows\System32\cmd.exe
               FileVersion: 10.0.19041.746 (WinBuild.160101.0800)
               Description: Windows Command Processor
               Product: Microsoft® Windows® Operating System
               Company: Microsoft Corporation
               OriginalFileName: Cmd.Exe
               CommandLine: cmd.exe /c whoami
               CurrentDirectory: C:\ProgramData\
               User: DESKTOP-R4PEEIF\waldo
               LogonGuid: {67e39d39-ed25-6269-7000-170000000000}
               LogonId: 0x170070
               TerminalSessionId: 1
               IntegrityLevel: Medium
               Hashes: SHA1=F1EFB0FDDC156E4C61C5F78A54700E4E7984D55D,MD5=8A2122E8162DBEF04694B9C3E0B6CDEE,SHA256=B99D61D874728EDC0918CA0EB10EAB93D381E7367E377406E65963366C874450,IMPHASH=272245E2988E1E430500B852C4FB5E18
               ParentProcessGuid: {67e39d39-f935-6269-8203-000000000300}
               ParentProcessId: 7780
               ParentImage: C:\Windows\System32\WerFault.exe
               ParentCommandLine: "C:\\Windows\\System32\\werfault.exe"
               ParentUser: DESKTOP-R4PEEIF\waldo
```

Answer: {hidden}