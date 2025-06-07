### Using Set and Echo to View Variables
1. Set - print all available environment variables on the system - changes are not permenant
2. Setx - To set persistent changes to the ENV, make the appropriate changes to the registry.
3. echo - you know what echo does
4. To remove variables, we cannot directly delete them like we would a file or directory; instead, we must clear their values by setting them equal to nothing. (`setx DCIP ""`)
[Other variables](https://ss64.com/nt/syntax-variables.html)

|Variable Name|Description|
|---|---|
|`%PATH%`|Specifies a set of directories(locations) where executable programs are located.|
|`%OS%`|The current operating system on the user's workstation.|
|`%SYSTEMROOT%`|Expands to `C:\Windows`. A system-defined read-only variable containing the Windows system folder. Anything Windows considers important to its core functionality is found here, including important data, core system binaries, and configuration files.|
|`%LOGONSERVER%`|Provides us with the login server for the currently active user followed by the machine's hostname. We can use this information to know if a machine is joined to a domain or workgroup.|
|`%USERPROFILE%`|Provides us with the location of the currently active user's home directory. Expands to `C:\Users\{username}`.|
|`%ProgramFiles%`|Equivalent of `C:\Program Files`. This location is where all the programs are installed on an `x64` based system.|
|`%ProgramFiles(x86)%`|Equivalent of `C:\Program Files (x86)`. This location is where all 32-bit programs running under `WOW64` are installed. Note that this variable is only accessible on a 64-bit host. It can be used to indicate what kind of host we are interacting with. (`x86` vs. `x64` architecture)|
### Scope

| Scope              | Description                                                                                                                                                                                                                                                                                                                                                                                       | Permissions Required to Access                                    | Registry Location                                                                 |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| `System (Machine)` | The System scope contains environment variables defined by the Operating System (OS) and are accessible globally by all users and accounts that log on to the system. The OS requires these variables to function properly and are loaded upon runtime.                                                                                                                                           | Local Administrator or Domain Administrator                       | `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment` |
| `User`             | The User scope contains environment variables defined by the currently active user and are only accessible to them, not other users who can log on to the same system.                                                                                                                                                                                                                            | Current Active User, Local Administrator, or Domain Administrator | `HKEY_CURRENT_USER\Environment`                                                   |
| `Process`          | The Process scope contains environment variables that are defined and accessible in the context of the currently running process. Due to their transient nature, their lifetime only lasts for the currently running process in which they were initially defined. They also inherit variables from the System/User Scopes and the parent process that spawns it (only if it is a child process). | Current Child Process, Parent Process, or Current Active User     | `None (Stored in Process Memory)`                                                 |
