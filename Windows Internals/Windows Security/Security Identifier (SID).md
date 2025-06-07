```powershell
PS C:\Users\andre> whoami /user   

USER INFORMATION               
----------------         

User Name   SID                                                                  =========== =============================================                        kevin\andre S-1-5-21-3941375616-3570008813-952051411-1001
```
A SID consists of the Identifier Authority and the Relative ID (RID). In an Active Directory (AD) domain environment, the SID also includes the domain SID.

This is the format of an SID
```powershell
(SID)-(revision level)-(identifier-authority)-(subauthority1)-(subauthority2)-(etc)
```
Let's break down the SID piece by piece.

| **Number**                      | **Meaning**          | **Description**                                                                                                                                                                                    |
| ------------------------------- | -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| S                               | SID                  | Identifies the string as a SID.                                                                                                                                                                    |
| 1                               | Revision Level       | To date, this has never changed and has always been `1`.                                                                                                                                           |
| 5                               | Identifier-authority | A 48-bit string that identifies the authority (the computer or network) that created the SID.                                                                                                      |
| 21                              | Subauthority1        | This is a variable number that identifies the user's relation or group described by the SID to the authority that created it. It tells us in what order this authority created the user's account. |
| 674899381-4069889467-2080702030 | Subauthority2        | Tells us which computer (or domain) created the number                                                                                                                                             |
| 1002                            | Subauthority3        | The RID that distinguishes one account from another. Tells us whether this user is a normal user, a guest, an administrator, or part of some other group                                           |
