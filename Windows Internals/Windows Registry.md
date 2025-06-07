The `Registry` can be considered a hierarchal tree that contains two essential elements: `keys` and `values`.
[Attack Techniques](https://attack.mitre.org/techniques/T1112/)
### What are Keys

`Keys`, in essence, are containers that represent a specific component of the PC. Keys can contain other keys and values as data. These entries can take many forms, and naming contexts only require that a Key be named using alphanumeric (printable) characters and is not case-sensitive.
### What Are Values

`Values` represent data in the form of objects that pertain to that specific Key. These values consist of a name, a type specification, and the required data to identify what it's for.
### Registry Hives
| **Name**            | **Abbreviation** | **Description**                                                                                                                                                                                                                                                                                                                     |
| ------------------- | ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| HKEY_LOCAL_MACHINE  | `HKLM`           | This subtree contains information about the computer's <mark>physical state</mark>, such as hardware and operating system data, bus types, memory, device drivers, and more.                                                                                                                                                        |
| HKEY_CURRENT_CONFIG | `HKCC`           | This section contains records for the host's <mark>current hardware profile.</mark> (shows the variance between current and default setups) Think of this as a redirection of the [HKLM](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc739525\(v=ws.10\)) CurrentControlSet profile key. |
| HKEY_CLASSES_ROOT   | `HKCR`           | Filetype information, UI extensions, and backward compatibility settings are defined here.                                                                                                                                                                                                                                          |
| HKEY_CURRENT_USER   | `HKCU`           | Value entries here define the specific OS and software settings for each specific user. `Roaming profile` settings, including user preferences, are stored under HKCU.                                                                                                                                                              |
| HKEY_USERS          | `HKU`            | The `default` User profile and current user configuration settings for the local computer are defined under HKU.                                                                                                                                                                                                                    |
### Why Is The Information Stored Within The Registry Important?

As a pentester, the Registry can be a treasure trove of information that can help us further our engagements. Everything from what software is installed, current OS revision, pertinent security settings, control of Defender, and more can be found in the Registry. From an offensive perspective, the Registry is hard for Defenders to protect. The hives are enormous and filled with hundreds of entries. Finding a singular change or addition among the hives is like hunting for a needle in a haystack (unless they keep solid backups of their configurations and host states). Having a general understanding of the Registry and where key values are within can help us take action quicker and for defenders spot any issues sooner.

#### Interacting with the registry via PowerShell
```powershell
Get-Item -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run | Select-Object -ExpandProperty Property
```
```powershell
Get-ChildItem -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Recurse
```
The below Registry key is used to `start` services/applications when a user `logs in` to the host. It is a great key to have visibility over and to keep in mind as a penetration tester.
```powershell
Get-ItemProperty -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```
```cmd
reg query HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip
```

```cmd
REG QUERY HKCU /F "password" /t REG_SZ /S /K
```
- `Reg query`: We are calling on Reg.exe and specifying that we want to query data.
- `HKCU`: This portion is setting the path to search. In this instance, we are looking in all of HKey_Current_User.
- `/f "password"`: /f sets the pattern we are searching for. In this instance, we are looking for "Password".
- `/t REG_SZ`: /t is setting the value type to search. If we do not specify, reg query will search through every type.
- `/s`: /s says to search through all subkeys and values recursively.
- `/k`: /k narrows it down to only searching through Key names.

```powershell
New-Item -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\ -Name TestKey
```
```powershell
New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name  "access" -PropertyType String -Value "C:\Users\htb-student\Downloads\payload.exe"
```
```powershell
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce\TestKey" /v access /t REG_SZ /d "C:\Users\htb-student\Downloads\payload.exe"
```
```powershell
Remove-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name  "access"
```
