[Microsoft Documentation](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands)
[ss64](https://ss64.com/nt/)
`Clear` screen = `cls`
`help ipconfig` OR `ipconfig /?`
`md`=`mkdir`
`rd`=`rmdir` <mark>/S</mark>
### [Doskey](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/doskey)
<mark>doskey /history</mark> Is the command to be used to get the history in a CMD

| **Key/Command** | **Description**                                                                                                            |
| :-------------: | -------------------------------------------------------------------------------------------------------------------------- |
| doskey /history | doskey /history will print the session's command history to the terminal or output it to a file when specified.            |
|     page up     | Places the first command in our session history to the prompt.                                                             |
|    page down    | Places the last command in history to the prompt.                                                                          |
|        ⇧        | Allows us to scroll up through our command history to view previously run commands.                                        |
|        ⇩        | Allows us to scroll down to our most recent commands run.                                                                  |
|        ⇨        | Types the previous command to prompt one character at a time.                                                              |
|        ⇦        | N/A                                                                                                                        |
|       F3        | Will retype the entire previous entry to our prompt.                                                                       |
|       F5        | Pressing F5 multiple times will allow you to cycle through previous commands.                                              |
|       F7        | Opens an interactive list of previous commands.                                                                            |
|       F9        | Enters a command to our prompt based on the number specified. The number corresponds to the commands place in our history. |
### Interesting Directories
| Name:               | Location:                            | Description:                                                                                                                                                                                                                                                                     |
| ------------------- | ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| %SYSTEMROOT%\Temp   | `C:\Windows\Temp`                    | Global directory containing temporary system files accessible to all users on the system. All users, regardless of authority, are provided full read, write, and execute permissions in this directory. Useful for dropping files as a low-privilege user on the system.         |
| %TEMP%              | `C:\Users\<user>\AppData\Local\Temp` | Local directory containing a user's temporary files accessible only to the user account that it is attached to. Provides full ownership to the user that owns this folder. Useful when the attacker gains control of a local/domain joined user account.                         |
| %PUBLIC%            | `C:\Users\Public`                    | Publicly accessible directory allowing any interactive logon account full access to read, write, modify, execute, etc., files and subfolders within the directory. Alternative to the global Windows Temp Directory as it's less likely to be monitored for suspicious activity. |
| %ProgramFiles%      | `C:\Program Files`                   | folder containing all 64-bit applications installed on the system. Useful for seeing what kind of applications are installed on the target system.                                                                                                                               |
| %ProgramFiles(x86)% | `C:\Program Files (x86)`             | Folder containing all 32-bit applications installed on the system. Useful for seeing what kind of applications are installed on the target system.                                                                                                                               |
### Modifying
1. Move
2. Robocopy
3. xcopy (deprecated in favor of Robocopy)
### List Files & View Their Contents
1. more
2. openfiles
3. type
### Create And Modify A File
1. echo
2. fsutil
3. ren
4. rename
5. replace
6. mkdir
### Input / Output
1. `>` is <mark>append</mark> (will create a file if it doesn't exist or overwrite)
2. `>>` is <mark>redirect</mark> (append)
3. `|` is <mark>pipe</mark>
4. `<` see below
![[Pasted image 20250509102145.png]]

5. `&` is <mark>Run A Then B</mark>
6. `&&` is <mark>State dependent</mark>
7. `||` is <mark>Run command A. If it fails, run command B.</mark>

#### Deleting Files
1. del (read only attribute `/A:R`) (Hidden attribute `/A:H`)
2. erase
3. rd /F

#### Misc
1. copy
2. move