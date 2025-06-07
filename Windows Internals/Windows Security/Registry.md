* This is a hierarchical database in Windows critical for the operating system. It stores low-level settings for the Windows operating system and applications that choose to use it.
* It is divided into computer-specific and user-specific data.
* The tree-structure consists of main folders (root keys) in which subfolders (subkeys) with their entries/files (values) are located.
* Each folder under Computer is a key.
* The root keys all start with HKEY.
* A key such as `HKEY-LOCAL-MACHINE` is abbreviated to `HKLM`. HKLM contains all settings that are relevant to the local system.
* This root key contains six subkeys like `SAM`, `SECURITY`, `SYSTEM`, `SOFTWARE`, `HARDWARE`, and `BCD`, loaded into memory at boot time (except `HARDWARE` which is dynamically loaded).
* You can find these under `C:\Windows\System32\Config\`.
* The user-specific registry hive (HKCU) is stored in the user folder (i.e., `C:\Users\<USERNAME>\Ntuser.dat`).
* There are 11 different types of values that can be entered in a subkey.

| **Value**               | **Type**                                                                                                                                                                                                                                                                                                                                                                                                              |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| REG_BINARY              | Binary data in any form.                                                                                                                                                                                                                                                                                                                                                                                              |
| REG_DWORD               | A 32-bit number.                                                                                                                                                                                                                                                                                                                                                                                                      |
| REG_DWORD_LITTLE_ENDIAN | A 32-bit number in little-endian format. Windows is designed to run on little-endian computer architectures. Therefore, this value is defined as REG_DWORD in the Windows header files.                                                                                                                                                                                                                               |
| REG_DWORD_BIG_ENDIAN    | A 32-bit number in big-endian format. Some UNIX systems support big-endian architectures.                                                                                                                                                                                                                                                                                                                             |
| REG_EXPAND_SZ           | A null-terminated string that contains unexpanded references to environment variables (for example, "%PATH%"). It will be a Unicode or ANSI string depending on whether you use the Unicode or ANSI functions. To expand the environment variable references, use the [**ExpandEnvironmentStrings**](https://docs.microsoft.com/en-us/windows/win32/api/processenv/nf-processenv-expandenvironmentstringsa) function. |
| REG_LINK                | A null-terminated Unicode string containing the target path of a symbolic link created by calling the [**RegCreateKeyEx**](https://docs.microsoft.com/en-us/windows/desktop/api/Winreg/nf-winreg-regcreatekeyexa) function with REG_OPTION_CREATE_LINK.                                                                                                                                                               |
| REG_MULTI_SZ            | A sequence of null-terminated strings, terminated by an empty string (\0). The following is an example: _String1_\0_String2_\0_String3_\0_LastString_\0\0 The first \0 terminates the first string, the second to the last \0 terminates the last string, and the final \0 terminates the sequence. Note that the final terminator must be factored into the length of the string.                                    |
| REG_NONE                | No defined value type.                                                                                                                                                                                                                                                                                                                                                                                                |
| REG_QWORD               | A 64-bit number.                                                                                                                                                                                                                                                                                                                                                                                                      |
| REG_QWORD_LITTLE_ENDIAN | A 64-bit number in little-endian format. Windows is designed to run on little-endian computer architectures. Therefore, this value is defined as REG_QWORD in the Windows header files.                                                                                                                                                                                                                               |
| REG_SZ                  | A null-terminated string. This will be either a Unicode or an ANSI string, depending on whether you use the Unicode or ANSI functions.                                                                                                                                                                                                                                                                                |
[Source](https://docs.microsoft.com/en-us/windows/win32/sysinfo/registry-value-types)

### Run and RunOnce Registry Keys
The hives which contain a logical group of keys, subkeys, and values to support software and files loaded into memory when the operating system is started or a user logs in are useful for maintaining access to the system.

```powershell
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce
```
Example of the `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run`
![[Pasted image 20250507074337.png]]

Example of the `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run`
![[Pasted image 20250507074402.png]]

