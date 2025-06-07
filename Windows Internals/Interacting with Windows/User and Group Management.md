Four different types of user accounts:
1. Service Accounts
2. Built-in accounts
3. Local users
4. Domain users
#### Built-In Accounts

| **Account**           | **Description**                                                                                                                                                  |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Administrator`       | This account is used to accomplish administrative tasks on the local host.                                                                                       |
| `Default Account`     | The default account is used by the system for running multi-user auth apps like the Xbox utility.                                                                |
| `Guest Account`       | This account is a limited rights account that allows users without a normal user account to access the host. It is disabled by default and should stay that way. |
| `WDAGUtility Account` | This account is in place for the Defender Application Guard, which can sandbox application sessions.                                                             |
* Get-LocalUser 
* New-LocalUser -Name "JLawrence" -NoPassword
```powershell
PS C:\htb> $Password = Read-Host -AsSecureString
****************
PS C:\htb> Set-LocalUser -Name "JLawrence" -Password $Password -Description "CEO EagleFang"
```
* `Get-LocalGroup`
* Add-LocalGroupMember -Group "Remote Desktop Users" -Member "JLawrence"

#### Remote System Administration Tools (RSAT)
* Get-WindowsCapability -Name RSAT* -Online | Add-WindowsCapability -Online
* Get-Module -Name ActiveDirectory -ListAvailable 
* Get-ADUser -Filter *
* Get-ADUser -Filter "GivenName -like 'Robert*'"
* Get-ADUser -Identity TSilver
![[Pasted image 20250510212910.png]]

* `New-ADUser -Name "MTanaka" -Surname "Tanaka" -GivenName "Mori" -Office "Security" -OtherAttributes @{'title'="Sensei";'mail'="MTanaka@greenhorn.corp"} -Accountpassword (Read-Host -AsSecureString "AccountPassword") -Enabled $true`
![[Pasted image 20250510213201.png]]
* `Set-ADUser -Identity MTanaka -Description " Sensei to Security Analyst's Rocky, Colt, and Tum-Tum"`
