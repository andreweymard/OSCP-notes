#### Permission Types Explained

- `Full Control`: Full Control allows for the user or group specified the ability to interact with the file as they see fit. This includes everything below, changing the permissions, and taking ownership of the file.
- `Modify`: Allows reading, writing, and deleting files and folders.
- `List Folder Contents`: This makes viewing and listing folders and subfolders possible along with executing files. This only applies to `folders`.
- `Read and Execute`: Allows users to view the contents within files and run executables (.ps1, .exe, .bat, etc.)
- `Write`: Write allows a user the ability to create new files and subfolders along with being able to add content to files.
- `Read`: Allows for viewing and listing folders and subfolders and viewing a file's contents.
- `Traverse Folder`: Traverse allows us to give a user the ability to access files or subfolders within a tree but not have access to the higher-level folder's contents. This is a way to provide selective access from a security perspective.