* In a domain environment, group policies are pushed down from a Domain Controller onto all domain-joined machines that Group Policy objects (GPOs) are linked to.
* Local Group Policy can be used to tweak certain graphical and network settings that are otherwise not accessible via the Control Panel.
* It can also be used to lock down an individual computer policy with stringent security settings, such as only allowing certain programs to be installed/run or enforcing strict user account password requirements.
* Local Group Policy Editoris split into two categories under Local Computer Policy - `Computer Configuration` and `User Configuration.`

For example, we can open the Local Computer Policy to enable Credential Guard by enabling the setting `Turn On Virtualization Based Security`. Credential Guard is a feature in Windows 10 that protects against credential theft attacks by isolating the operating system's LSA process.

![Turn On Virtualization Based Security settings window with options for enabling Secure Boot and DMA Protection.](https://academy.hackthebox.com/storage/modules/49/credguard.png)
