### NTFS Permissions
| Permission Type      | Description                                                                                                                                                                                                                                                                                                                                                                                |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Full Control         | Allows reading, writing, changing, deleting of files/folders.                                                                                                                                                                                                                                                                                                                              |
| Modify               | Allows reading, writing, and deleting of files/folders.                                                                                                                                                                                                                                                                                                                                    |
| List Folder Contents | Allows for viewing and listing folders and subfolders as well as executing files. Folders only inherit this permission.                                                                                                                                                                                                                                                                    |
| Read and Execute     | Allows for viewing and listing files and subfolders as well as executing files. Files and folders inherit this permission.                                                                                                                                                                                                                                                                 |
| Write                | Allows for adding files to folders and subfolders and writing to a file.                                                                                                                                                                                                                                                                                                                   |
| Read                 | Allows for viewing and listing of folders and subfolders and viewing a file's contents.                                                                                                                                                                                                                                                                                                    |
| Traverse Folder      | This allows or denies the ability to move through folders to reach other files or folders. For example, a user may not have permission to list the directory contents or view files in the documents or web apps directory in this example c:\users\bsmith\documents\webapps\backups\backup_02042020.zip but with Traverse Folder permissions applied, they can access the backup archive. |
### Integrity Control Access Control List (icacls)
[icacls Manual](https://ss64.com/nt/icacls.html)
``` Powershell
PS C:\Users\andre> icacls D:\Code\                                               D:\Code\ BUILTIN\Administrators:(I)(F)                                                    BUILTIN\Administrators:(I)(OI)(CI)(IO)(F)                                        NT AUTHORITY\SYSTEM:(I)(F)                                                       NT AUTHORITY\SYSTEM:(I)(OI)(CI)(IO)(F)                                           NT AUTHORITY\Authenticated Users:(I)(M)                                          NT AUTHORITY\Authenticated Users:(I)(OI)(CI)(IO)(M)                              BUILTIN\Users:(I)(RX)                                                            BUILTIN\Users:(I)(OI)(CI)(IO)(GR,GE)                                                                                                                  Successfully processed 1 files; Failed processing 0 files
```
- `(CI)`: container inherit
- `(OI)`: object inherit
- `(IO)`: inherit only
- `(NP)`: do not propagate inherit
- `(I)`: permission inherited from parent container- `
-   F : full access
-  `D` :  delete access
-  `N` :  no access
-  `M` :  modify access
-  `RX` :  read and execute access
-  `R` :  read-only access
-  `W` :  write-only access

