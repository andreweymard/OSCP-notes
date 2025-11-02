Sysmon uses an XML-based configuration file. The configuration file allows you to include or exclude certain types of events based on different attributes like process names, IP addresses, etc. We can refer to popular examples of useful Sysmon configuration files:

- For a comprehensive configuration, we can visit: [https://github.com/SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config). <-- `We will use this one in this section!`
- Another option is: [https://github.com/olafhartong/sysmon-modular](https://github.com/olafhartong/sysmon-modular), which provides a modular approach.

To get started, you can install Sysmon by downloading it from the official Microsoft documentation ([https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)). Once downloaded, open an administrator command prompt and execute the following command to install Sysmon.

## Detection Example 1: Detecting DLL Hijacking

In our specific use case, we aim to detect a DLL hijack. The Sysmon event log IDs relevant to DLL hijacks can be found in the Sysmon documentation ([https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)). To detect a DLL hijack, we need to focus on `Event Type 7`, which corresponds to module load events. To achieve this, we need to modify the `sysmonconfig-export.xml` Sysmon configuration file we downloaded from [https://github.com/SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config).

By examining the modified configuration, we can observe that the "include" comment signifies events that should be included.

![XML snippet showing RuleGroup with ImageLoad set to 'include' and a note about no rules meaning nothing will be logged.](https://academy.hackthebox.com/storage/modules/216/image14.png)

In the case of detecting DLL hijacks, we change the "include" to "exclude" to ensure that nothing is excluded, allowing us to capture the necessary data.

![XML snippet with RuleGroup, ImageLoad set to 'exclude', and a note about using 'include' with no rules.](https://academy.hackthebox.com/storage/modules/216/image15.png)

To utilize the updated Sysmon configuration, execute the following.

Analyzing Evil With Sysmon & Event Logs

```cmd-session
C:\Tools\Sysmon> sysmon.exe -c sysmonconfig-export.xml
```

![Command prompt showing Sysmon v13.33 loading sysmonconfig-export.xml, configuration validated and updated.](https://academy.hackthebox.com/storage/modules/216/image16.png)

With the modified Sysmon configuration, we can start observing image load events. To view these events, navigate to the Event Viewer and access "Applications and Services" -> "Microsoft" -> "Windows" -> "Sysmon." A quick check will reveal the presence of the targeted event ID.

![Sysmon event log showing multiple Information entries for Event ID 7, Image loaded.](https://academy.hackthebox.com/storage/modules/216/image17.png)

Let's now see how a Sysmon event ID 7 looks like.

![Sysmon log entry: Image loaded, ProcessID 8060, Image mmc.exe, ImageLoaded psapi.dll, Signed true, User DESKTOP-N33HELB\Waldo.](https://academy.hackthebox.com/storage/modules/216/image18.png)

The event log contains the DLL's signing status (in this case, it is Microsoft-signed), the process or image responsible for loading the DLL, and the specific DLL that was loaded. In our example, we observe that "MMC.exe" loaded "psapi.dll", which is also Microsoft-signed. Both files are located in the System32 directory.

Now, let's proceed with building a detection mechanism. To gain more insights into DLL hijacks, conducting research is paramount. We stumble upon an informative [blog post](https://www.wietzebeukema.nl/blog/hijacking-dlls-in-windows) that provides an exhaustive list of various DLL hijack techniques. For the purpose of our detection, we will focus on a specific hijack involving the vulnerable executable calc.exe and a list of DLLs that can be hijacked.

![Table showing calc.exe with associated DLLs: CRYPTBASE.DLL, edputil.dll, MLANG.dll, PROPSYS.dll, Secur32.dll, SSPICLI.DLL, WININET.dll, and their functions.](https://academy.hackthebox.com/storage/modules/216/image19.png)

Let's attempt the hijack using "calc.exe" and "WININET.dll" as an example. To simplify the process, we can utilize Stephen Fewer's "hello world" [reflective DLL](https://github.com/stephenfewer/ReflectiveDLLInjection/tree/master/bin). It should be noted that DLL hijacking does not require reflective DLLs.

By following the required steps, which involve renaming `reflective_dll.x64.dll` to `WININET.dll`, moving `calc.exe` from `C:\Windows\System32` along with `WININET.dll` to a writable directory (such as the `Desktop` folder), and executing `calc.exe`, we achieve success. Instead of the Calculator application, a [MessageBox](https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-messageboxa) is displayed.

![Command prompt running calc.exe, desktop showing WININET.dll and calc icons, with a popup message 'Hello from DllMain!' indicating Reflective DLL Injection.](https://academy.hackthebox.com/storage/modules/216/image20.png)

Next, we analyze the impact of the hijack. First, we filter the event logs to focus on `Event ID 7`, which represents module load events, by clicking "Filter Current Log...".

![Filter Current Log window with options for event level, event logs set to Microsoft-Windows-Sysmon/Operational, and Event ID 7.](https://academy.hackthebox.com/storage/modules/216/image21.png)

Subsequently, we search for instances of "calc.exe", by clicking "Find...", to identify the DLL load associated with our hijack.

![Sysmon log entry: Image loaded, ProcessID 6212, Image calc.exe, ImageLoaded WININET.dll, Signed false, User DESKTOP-N33HELB\Waldo. Find dialog open for 'calc.exe'.](https://academy.hackthebox.com/storage/modules/216/image43.png)

The output from Sysmon provides valuable insights. Now, we can observe several indicators of compromise (IOCs) to create effective detection rules. Before moving forward though, let's compare this to an authenticate load of "wininet.dll" by "calc.exe".

![Sysmon log entry: Image loaded, ProcessID 5464, Image calc.exe, ImageLoaded wininet.dll, Signed true, User DESKTOP-N33HELB\Waldo. Find dialog open for 'calc.exe'.](https://academy.hackthebox.com/storage/modules/216/image23.png)

Let's explore these IOCs:

1. "calc.exe", originally located in System32, should not be found in a writable directory. Therefore, a copy of "calc.exe" in a writable directory serves as an IOC, as it should always reside in System32 or potentially Syswow64.

2. "WININET.dll", originally located in System32, should not be loaded outside of System32 by calc.exe. If instances of "WININET.dll" loading occur outside of System32 with "calc.exe" as the parent process, it indicates a DLL hijack within calc.exe. While caution is necessary when alerting on all instances of "WININET.dll" loading outside of System32 (as some applications may package specific DLL versions for stability), in the case of "calc.exe", we can confidently assert a hijack due to the DLL's unchanging name, which attackers cannot modify to evade detection.

3. The original "WININET.dll" is Microsoft-signed, while our injected DLL remains unsigned.

These three powerful IOCs provide an effective means of detecting a DLL hijack involving calc.exe. It's important to note that while Sysmon and event logs offer valuable telemetry for hunting and creating alert rules, they are not the sole sources of information.

## Detection Example 2: Detecting Unmanaged PowerShell/C-Sharp Injection

Before delving into detection techniques, let's gain a brief understanding of C# and its runtime environment. C# is considered a "managed" language, meaning it requires a backend runtime to execute its code. The [Common Language Runtime (CLR)](https://docs.microsoft.com/en-us/dotnet/standard/clr) serves as this runtime environment. [Managed code](https://docs.microsoft.com/en-us/dotnet/standard/managed-code) does not directly run as assembly; instead, it is compiled into a bytecode format that the runtime processes and executes. Consequently, a managed process relies on the CLR to execute C# code.

As defenders, we can leverage this knowledge to detect unusual C# injections or executions within our environment. To accomplish this, we can utilize a useful utility called [Process Hacker](https://processhacker.sourceforge.io/).

![Task Manager showing processes like Microsoft.Photos.exe, msedge.exe, powershell.exe, ProcessHacker.exe, with CPU and memory usage details.](https://academy.hackthebox.com/storage/modules/216/image24.png)

By using Process Hacker, we can observe a range of processes within our environment. Sorting the processes by name, we can identify interesting color-coded distinctions. Notably, "powershell.exe", a managed process, is highlighted in green compared to other processes. Hovering over powershell.exe reveals the label "Process is managed (.NET)," confirming its managed status.

![Task Manager tooltip for powershell.exe, showing file path, version 10.0.19041.546, signed by Microsoft, console host conhost.exe (5092).](https://academy.hackthebox.com/storage/modules/216/image25.png)

Examining the module loads for `powershell.exe`, by right-clicking on `powershell.exe`, clicking "Properties", and navigating to "Modules", we can find relevant information.

![Image showing clr.dll and drjit.dll with memory addresses, sizes, and descriptions for Microsoft .NET Runtime components.](https://academy.hackthebox.com/storage/modules/216/image26.png)

The presence of "Microsoft .NET Runtime...", `clr.dll`, and `clrjit.dll` should attract our attention. These 2 DLLs are used when C# code is ran as part of the runtime to execute the bytecode. If we observe these DLLs loaded in processes that typically do not require them, it suggests a potential [execute-assembly](https://www.cobaltstrike.com/blog/cobalt-strike-3-11-the-snake-that-eats-its-tail/) or [unmanaged PowerShell injection](https://www.youtube.com/watch?v=7tvfb9poTKg&ab_channel=RaphaelMudge) attack.

To showcase unmanaged PowerShell injection, we can inject an [unmanaged PowerShell-like DLL](https://github.com/leechristensen/UnmanagedPowerShell) into a random process, such as `spoolsv.exe`. We can do that by utilizing the [PSInject project](https://github.com/EmpireProject/PSInject) in the following manner.

Analyzing Evil With Sysmon & Event Logs

```powershell-session
 powershell -ep bypass
 Import-Module .\Invoke-PSInject.ps1
 Invoke-PSInject -ProcId [Process ID of spoolsv.exe] -PoshCode "V3JpdGUtSG9zdCAiSGVsbG8sIEd1cnU5OSEi"
```

![Tooltip for spoolsv.exe showing file path, version 10.0.19041.1288, signed by Microsoft, and associated with Print Spooler service.](https://academy.hackthebox.com/storage/modules/216/image27.png)

After the injection, we observe that "spoolsv.exe" transitions from an unmanaged to a managed state.

![Tooltip for spoolsv.exe showing file path, version 10.0.19041.1288, signed by Microsoft, associated with Print Spooler service, and managed by .NET.](https://academy.hackthebox.com/storage/modules/216/image28.png)

Additionally, by referring to both the related "Modules" tab of Process Hacker and Sysmon `Event ID 7`, we can examine the DLL load information to validate the presence of the aforementioned DLLs.

![Properties window for spoolsv.exe showing modules like advapi32.dll, amsi.dll, APMon.dll, with base addresses, sizes, and descriptions.](https://academy.hackthebox.com/storage/modules/216/image29.png) ![Sysmon Event 7: Image loaded, ProcessID 2792, Image spoolsv.exe, ImageLoaded clr.dll, Microsoft .NET Runtime, signed by Microsoft, User NT AUTHORITY\SYSTEM.](https://academy.hackthebox.com/storage/modules/216/image30.png)

## Detection Example 3: Detecting Credential Dumping

Another critical aspect of cybersecurity is detecting credential dumping activities. One widely used tool for credential dumping is [Mimikatz](https://github.com/gentilkiwi/mimikatz), offering various methods for extracting Windows credentials. One specific command, "sekurlsa::logonpasswords", enables the dumping of password hashes or plaintext passwords by accessing the [Local Security Authority Subsystem Service (LSASS)](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service). LSASS is responsible for managing user credentials and is a primary target for credential-dumping tools like Mimikatz.

The attack can be executed as follows.

Analyzing Evil With Sysmon & Event Logs

```cmd-session
C:\Tools\Mimikatz> mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #18362 Feb 29 2020 11:13:36
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > http://pingcastle.com / http://mysmartlogon.com   ***/

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # sekurlsa::logonpasswords

Authentication Id : 0 ; 1128191 (00000000:001136ff)
Session           : RemoteInteractive from 2
User Name         : Administrator
Domain            : DESKTOP-NU10MTO
Logon Server      : DESKTOP-NU10MTO
Logon Time        : 5/31/2023 4:14:41 PM
SID               : S-1-5-21-2712802632-2324259492-1677155984-500
        msv :
         [00000003] Primary
         * Username : Administrator
         * Domain   : DESKTOP-NU10MTO
         * NTLM     : XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
         * SHA1     : XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX0812156b
        tspkg :
        wdigest :
         * Username : Administrator
         * Domain   : DESKTOP-NU10MTO
         * Password : (null)
        kerberos :
         * Username : Administrator
         * Domain   : DESKTOP-NU10MTO
         * Password : (null)
        ssp :   KO
        credman :
```

As we can see, the output of the "sekurlsa::logonpasswords" command provides powerful insights into compromised credentials.

To detect this activity, we can rely on a different Sysmon event. Instead of focusing on DLL loads, we shift our attention to process access events. By checking `Sysmon event ID 10`, which represents "ProcessAccess" events, we can identify any suspicious attempts to access LSASS.

![Event ID 10: ProcessAccess details process access events for detecting hacking tools targeting processes like Lsass.exe for credential theft.](https://academy.hackthebox.com/storage/modules/216/image32.png) ![Sysmon Event 10: Process accessed, SourceImage AgentEXE.exe, TargetImage lsass.exe, SourceUser DESKTOP-R4PEEIF\waldo, TargetUser NT AUTHORITY\SYSTEM.](https://academy.hackthebox.com/storage/modules/216/image33.png)

For instance, if we observe a random file ("AgentEXE" in this case) from a random folder ("Downloads" in this case) attempting to access LSASS, it indicates unusual behavior. Additionally, the `SourceUser` being different from the `TargetUser` (e.g., "waldo" as the `SourceUser` and "SYSTEM" as the `TargetUser`) further emphasizes the abnormality. It's also worth noting that as part of the mimikatz-based credential dumping process, the user must request [SeDebugPrivileges](https://devblogs.microsoft.com/oldnewthing/20080314-00/?p=23113). As the name suggests, it's primarily used for debugging. This can be another Indicator of Compromise (IOC).

Please note that some legitimate processes may access LSASS, such as authentication-related processes or security tools like AV or EDR.
