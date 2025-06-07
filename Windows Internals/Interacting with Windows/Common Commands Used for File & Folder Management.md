| **Command**      | **Alias**        | **Description**                                                                                         |
| ---------------- | ---------------- | ------------------------------------------------------------------------------------------------------- |
| `Get-Item`       | gi               | Retrieve an object (could be a file, folder, registry object, etc.)                                     |
| `Get-ChildItem`  | ls / dir / gci   | Lists out the content of a folder or registry hive.                                                     |
| `New-Item`       | md / mkdir / ni  | Create new objects. ( can be files, folders, symlinks, registry entries, and more)                      |
| `Set-Item`       | si               | Modify the property values of an object.                                                                |
| `Copy-Item`      | copy / cp / ci   | Make a duplicate of the item.                                                                           |
| `Rename-Item`    | ren / rni        | Changes the object name.                                                                                |
| `Remove-Item`    | rm / del / rmdir | Deletes the object.                                                                                     |
| `Get-Content`    | cat / type       | Displays the content within a file or object.                                                           |
| `Add-Content`    | ac               | Append content to a file.                                                                               |
| `Set-Content`    | sc               | overwrite any content in a file with new data.                                                          |
| `Clear-Content`  | clc              | Clear the content of the files without deleting the file itself.                                        |
| `Compare-Object` | diff / compare   | Compare two or more objects against each other. This includes the object itself and the content within. |
Example:
`get-childitem -Path *.txt | rename-item -NewName {$_.name -replace ".txt",".md"}`
