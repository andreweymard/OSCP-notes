### Uses
1. Provisioning servers and installing server roles
2. Creating Active Directory user accounts for new employees
3. Managing Active Directory group permissions
4. Disabling and deleting Active Directory user accounts
5. Managing file share permissions
6. Interacting with Azure AD and Azure VMs
7. Creating, deleting, and monitoring directories & files
8. Gathering information about workstations and servers
9. Setting up Microsoft Exchange email inboxes for users (in the cloud &/or on-premises)
### Cmdlets
* These are small single-function tools built into the shell. There are more than 100 core cmdlets, and many additional ones have been written, or we can author our own to perform more complex tasks.
* "a single-feature command that manipulates objects in PowerShell." - [Microsoft](https://docs.microsoft.com/en-us/powershell/scripting/lang-spec/chapter-13?view=powershell-7.2)
### Verb-Noun
* Cmdlets are in the form of `Verb-Noun`. For example, the command `Get-ChildItem.`
* `Get-Command -verb get`
* `Get-Command -noun windows*`
* `Get-Command -Verb Get -Noun *image*`
### History
* Get-History (Only stores session history)
* <mark>!</mark> in bash with the number executes that numbered command in history, the equivalent in PS is <mark>r</mark>
* <mark>! 32 </mark> == <mark>r 32</mark>
* `type $env:APPDATA\Microsoft\Windows\Powershell\PSReadLine\ConsoleHost_history.txt | more` (This stores persistent history except see below)
* `PSReadline` will automatically attempt to filter any entries that include the strings:
	- `password`
	- `asplaintext`
	- `token`
	- `apikey`
	- `secret`
#### Hotkeys

|**HotKey**|**Description**|
|---|---|
|`CTRL+R`|It makes for a searchable history. We can start typing after, and it will show us results that match previous commands.|
|`CTRL+L`|Quick screen clear.|
|`CTRL+ALT+Shift+?`|This will print the entire list of keyboard shortcuts PowerShell will recognize.|
|`Escape`|When typing into the CLI, if you wish to clear the entire line, instead of holding backspace, you can just hit `escape`, which will erase the line.|
|`↑`|Scroll up through our previous history.|
|`↓`|Scroll down through our previous history.|
|`F7`|Brings up a TUI with a scrollable interactive history from our session.|
### [[Powershell modules]]
A [PowerShell module](https://docs.microsoft.com/en-us/powershell/scripting/developer/module/understanding-a-windows-powershell-module?view=powershell-7.2) is structured PowerShell code that is made easy to use & share. As mentioned in the official Microsoft docs, a module can be made up of the following:
- Cmdlets
- Script files
- Functions
- Assemblies
- Related resources (manifests and help files)

#### Get-Module
* Get-Module -ListAvailable 
* Import-Module .\PowerSploit.psd1
* Find-Module -Name AdminToolbox | Install-Module
#### Tools To Be Aware Of
Below we will quickly list a few PowerShell modules and projects we, as penetration testers and sysadmins, should be aware of. Each of these tools brings a new capability to use within PowerShell. Of course, there are plenty more than just our list; these are just several we find ourselves returning to on every engagement.
- [AdminToolbox](https://www.powershellgallery.com/packages/AdminToolbox/11.0.8): AdminToolbox is a collection of helpful modules that allow system administrators to perform any number of actions dealing with things like Active Directory, Exchange, Network management, file and storage issues, and more.
- [ActiveDirectory](https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps): This module is a collection of local and remote administration tools for all things Active Directory. We can manage users, groups, permissions, and much more with it.
- [Empire / Situational Awareness](https://github.com/BC-SECURITY/Empire/tree/master/empire/server/data/module_source/situational_awareness): Is a collection of PowerShell modules and scripts that can provide us with situational awareness on a host and the domain they are apart of. This project is being maintained by [BC Security](https://github.com/BC-SECURITY) as a part of their Empire Framework.
- [Inveigh](https://github.com/Kevin-Robertson/Inveigh): Inveigh is a tool built to perform network spoofing and Man-in-the-middle attacks.
- [BloodHound / SharpHound](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors): Bloodhound/Sharphound allows us to visually map out an Active Directory Environment using graphical analysis tools and data collectors written in C# and PowerShell.

### Execution Policy

| **Policy**     | **Description**                                                                                                                                                                                                                                                  |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `AllSigned`    | All scripts can run, but a trusted publisher must sign scripts and configuration files. This includes both remote and local scripts. We receive a prompt before running scripts signed by publishers that we have not yet listed as either trusted or untrusted. |
| `Bypass`       | No scripts or configuration files are blocked, and the user receives no warnings or prompts.                                                                                                                                                                     |
| `Default`      | This sets the default execution policy, `Restricted` for Windows desktop machines and `RemoteSigned` for Windows servers.                                                                                                                                        |
| `RemoteSigned` | Scripts can run but requires a digital signature on scripts that are downloaded from the internet. Digital signatures are not required for scripts that are written locally.                                                                                     |
| `Restricted`   | This allows individual commands but does not allow scripts to be run. All script file types, including configuration files (`.ps1xml`), module script files (`.psm1`), and PowerShell profiles (`.ps1`) are blocked.                                             |
| `Undefined`    | No execution policy is set for the current scope. If the execution policy for ALL scopes is set to undefined, then the default execution policy of `Restricted` will be used.                                                                                    |
| `Unrestricted` | This is the default execution policy for non-Windows computers, and it cannot be changed. This policy allows for unsigned scripts to be run but warns the user before running scripts that are not from the local intranet zone.                                 |
```powershell-session
Set-ExecutionPolicy -scope Process
```

## Explanation of PowerShell Output (Objects Explained)

With PowerShell, not everything is generic text strings like in Bash or cmd. In PowerShell, everything is an `Object`. However, what is an object? Let us examine this concept further:

`What is an Object?` An `object` is an `individual` instance of a `class` within PowerShell. Let us use the example of a computer as our object. The total of everything (parts, time, design, software, etc.) makes a computer a computer.

`What is a Class?` A class is the `schema` or 'unique representation of a thing (object) and how the sum of its `properties` define it. The `blueprint` used to lay out how that computer should be assembled and what everything within it can be considered a Class.

`What are Properties?` Properties are simply the `data` associated with an object in PowerShell. For our example of a computer, the individual `parts` that we assemble to make the computer are its properties. Each part serves a purpose and has a unique use within the object.

`What are Methods?` Simply put, methods are all the functions our object has. Our computer allows us to process data, surf the internet, learn new skills, etc. All of these are the methods for our object.

#### Filter Output
`get-service | Select-Object -Property DisplayName,Name,Status | Sort-Object DisplayName | fl`
`Get-Service | where DisplayName -like '*Defender*'`

`Get-Childitem –Path C:\Users\MTanaka\ -File -Recurse -ErrorAction SilentlyContinue | where {($_.Name -like "*.txt" -or $_.Name -like "*.py" -or $_.Name -like "*.ps1" -or $_.Name -like "*.md" -or $_.Name -like "*.csv")}`

`Get-ChildItem -Path C:\Users\MTanaka\ -Filter "*.txt" -Recurse -File | sls "Password","credential","key"`

```powershell
C:\htb> Get-ChildItem -Path C:\Users\MTanaka\ -Filter "*.txt" -Recurse -File | sls "Password","credential","key"

CFP-Notes.txt:99:Lazzaro, N. (2004). Why we play games: Four keys to more emotion without story. Retrieved from:
notes.txt:3:- Password: F@ll2022!
wmic.txt:67:  wmic netlogin get name,badpasswordcount
wmic.txt:69:Are the screensavers password protected? What is the timeout? good use: see that all systems are
complying with policy evil use: find systems to walk up and use (assuming physical access is an option)
```

```powershell
C:\htb> Get-Childitem –Path C:\Users\MTanaka\ -File -Recurse -ErrorAction SilentlyContinue | where {($_. Name -like "*.txt" -or $_. Name -like "*.py" -or $_. Name -like "*.ps1" -or $_. Name -like "*.md" -or $_. Name -like "*.csv")} | sls "Password","credential","key","UserName"

New-PC-Setup.md:56:  - getting your vpn key
CFP-Notes.txt:99:Lazzaro, N. (2004). Why we play games: Four keys to more emotion without story. Retrieved from:
notes.txt:3:- Password: F@ll2022!
wmic.txt:54:  wmic computersystem get username
wmic.txt:67:  wmic netlogin get name,badpasswordcount
wmic.txt:69:Are the screensavers password protected? What is the timeout? good use: see that all systems are
complying with policy evil use: find systems to walk up and use (assuming physical access is an option)
wmic.txt:83:  wmic netuse get Name,username,connectiontype,localname
```

#### Comparison Operators

|**Expression**|**Description**|
|---|---|
|`Like`|Like utilizes wildcard expressions to perform matching. For example, `'*Defender*'` would match anything with the word Defender somewhere in the value.|
|`Contains`|Contains will get the object if any item in the property value matches exactly as specified.|
|`Equal` to|Specifies an exact match (case sensitive) to the property value supplied.|
|`Match`|Is a regular expression match to the value supplied.|
|`Not`|specifies a match if the property is `blank` or does not exist. It will also match `$False`.|
#### Helpful Directories to Check
The Console History files kept by the host are an endless well of information, especially if you land on an administrator's host. You can check two different points:

- `C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt`
- `Get-Content (Get-PSReadlineOption).HistorySavePath`

`invoke-command -ComputerName ACADEMY-ICL-DC,LOCALHOST -ScriptBlock {Get-Service -Name 'windefend'}`

#### [[Windows Networking]]

#### PowerShell Extensions

| **Extension** | **Description**                                                                                                                 |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| ps1           | The `*.ps1` file extension represents executable PowerShell scripts.                                                            |
| psm1          | The `*.psm1` file extension represents a PowerShell module file. It defines what the module is and what is contained within it. |
| psd1          | The `*.psd1` is a PowerShell data file detailing the contents of a PowerShell module in a table of key/value pairs.             |
