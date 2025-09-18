## Group Policy Objects (GPOs)

A [Group Policy Object (GPO)](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/policy/group-policy-objects) is a virtual collection of policy settings that can be applied to `user(s)` or `computer(s)`. GPOs include policies such as screen lock timeout, disabling USB ports, enforcing a custom domain password policy, installing software, managing applications, customizing remote access settings, and much more. Every GPO has a unique name and is assigned a unique identifier (a GUID). They can be linked to a specific OU, domain, or site. A single GPO can be linked to multiple containers, and any container can have multiple GPOs applied to it. They can be applied to individual users, hosts, or groups by being applied directly to an OU. Every GPO contains one or more Group Policy settings that may apply at the local machine level or within the Active Directory context.

---

## Example GPOs

Some examples of things we can do with GPOs may include:

- Establishing different password policies for service accounts, admin accounts, and standard user accounts using separate GPOs
- Preventing the use of removable media devices (such as USB devices)
- Enforcing a screensaver with a password
- Restricting access to applications that a standard user may not need, such as cmd.exe and PowerShell
- Enforcing audit and logging policies
- Blocking users from running certain types of programs and scripts
- Deploying software across a domain
- Blocking users from installing unapproved software
- Displaying a logon banner whenever a user logs into a system
- Disallowing LM hash usage in the domain
- Running scripts when computers start/shutdown or when a user logs in/out of their machine

Let's use as example a default Windows Server 2008 Active Directory implementation, password complexity is enforced by default. The password complexity requirements are as follows:

- Passwords must be at least 7 characters long.
- Passwords must contain characters from at least three of the following four categories:
    - Uppercase characters (A-Z)
    - Lowercase characters (a-z)
    - Numbers (0-9)
    - Special characters (e.g. !@#$%^&*()_+|~-=`{}[]:";'<>?,./)

These are just a few examples of what can be done with Group Policy. There are hundreds of settings that can be applied within a GPO, which can get extremely granular. For example, below are some options that we can set for Remote Desktop sessions.

#### RDP GPO Settings

![Group Policy Management Editor showing Remote Session Environment settings under Remote Desktop Services. Settings include RemoteFX for Windows Server 2008 R2 and various display and graphics configurations.](https://academy.hackthebox.com/storage/modules/74/rdp_settings.png)

GPO settings are processed using the hierarchical structure of AD and are applied using the `Order of Precedence` rule as seen in the table below:

#### Order of Precedence

|**Level**|**Description**|
|---|---|
|`Local Group Policy`|The policies are defined directly to the host locally outside the domain. Any setting here will be overwritten if a similar setting is defined at a higher level.|
|`Site Policy`|Any policies specific to the Enterprise Site that the host resides in. Remember that enterprise environments can span large campuses and even across countries. So it stands to reason that a site might have its own policies to follow that could differentiate it from the rest of the organization. Access Control policies are a great example of this. Say a specific building or `site` performs secret or restricted research and requires a higher level of authorization for access to resources. You could specify those settings at the site level and ensure they are linked so as not to be overwritten by domain policy. This is also a great way to perform actions like printer and share mapping for users in specific sites.|
|`Domain-wide Policy`|Any settings you wish to have applied across the domain as a whole. For example, setting the password policy complexity level, configuring a Desktop background for all users, and setting a Notice of Use and Consent to Monitor banner at the login screen.|
|`Organizational Unit` (OU)|These settings would affect users and computers who belong to specific OUs. You would want to place any unique settings here that are role-specific. For example, the mapping of a particular share drive that can only be accessed by HR, access to specific resources like printers, or the ability for IT admins to utilize PowerShell and command-prompt.|
|`Any OU Policies nested within other OU's`|Settings at this level would reflect special permissions for objects within nested OUs. For example, providing Security Analysts a specific set of Applocker policy settings that differ from the standard IT Applocker settings.|

We can manage Group Policy from the Group Policy Management Console (found under Administrative Tools in the Start Menu on a domain controller), custom applications, or using the PowerShell [GroupPolicy](https://docs.microsoft.com/en-us/powershell/module/grouppolicy/?view=windowsserver2022-ps) module via command line. The Default Domain Policy is the default GPO that is automatically created and linked to the domain. It has the highest precedence of all GPOs and is applied by default to all users and computers. Generally, it is best practice to use this default GPO to manage default settings that will apply domain-wide. The Default Domain Controllers policy is also created automatically with a domain and sets baseline security and auditing settings for all domain controllers in a given domain. It can be customized as needed, like any GPO.

---

## GPO Order of Precedence

GPOs are processed from the top down when viewing them from a domain organizational standpoint. A GPO linked to an OU at the highest level in an Active Directory network (at the domain level, for example) would be processed first, followed by those linked to a child OU, etc. This means that a GPO linked directly to an OU containing user or computer objects is processed last. In other words, a GPO attached to a specific OU would have precedence over a GPO attached at the domain level because it will be processed last and could run the risk of overriding settings in a GPO higher up in the domain hierarchy. One more thing to keep track of with precedence is that a setting configured in Computer policy will always have a higher priority of the same setting applied to a user. The following graphic illustrates precedence and how it is applied.

#### GPO Precedence Order

![Diagram of Group Policy processing order: Local Security Policy, Site Policy, Domain Policy, Parent OU Policy, Child OU Policy. Descriptions of Computer, User, and Group Policy settings application.](https://academy.hackthebox.com/storage/modules/74/gpo_levels.png)

Let's look at another example using the Group Policy Management Console on a Domain Controller. In this image, we see several GPOs. The `Disabled Forced Restarts` GPO will have precedence over the `Logon Banner` GPO since it would be processed last. Any settings configured in the `Disabled Forced Restarts` GPO could potentially override settings in any GPOs higher up in the hierarchy (including those linked to the `Corp` OU).

#### GPMC Hive Example

![Group Policy Management interface showing Corp OU under INLANEFREIGHT.LOCAL. Linked Group Policy Objects: Disallow LM Hash, Block Removable Media, Disable Guest Account, all enabled.](https://academy.hackthebox.com/storage/modules/74/gpo_precedence.png)

This image also shows an example of several GPOs being linked to the `Corp` OU. When more than one GPO is linked to an OU, they are processed based on the `Link Order`. The GPO with the lowest Link Order is processed last, or the GPO with link order 1 has the highest precedence, then 2, and 3, and so on. So in our example above, the `Disallow LM Hash` GPO will have precedence over the `Block Removable Media` and `Disable Guest Account` GPOs, meaning it will be processed first.

It is possible to specify the `Enforced` option to enforce settings in a specific GPO. If this option is set, policy settings in GPOs linked to lower OUs `CANNOT` override the settings. If a GPO is set at the domain level with the `Enforced` option selected, the settings contained in that GPO will be applied to all OUs in the domain and cannot be overridden by lower-level OU policies. In the past, this setting was called `No Override` and was set on the container in question under Active Directory Users and Computers. Below we can see an example of an `Enforced` GPO, where the `Logon Banner` GPO is taking precedence over GPOs linked to lower OUs and therefore will not be overridden.

#### Enforced GPO Policy Precedence

![Group Policy Management interface showing Service Accounts under INLANEFREIGHT.LOCAL. Linked Group Policy Objects: Logon Banner, Disallow LM Hash, Block Removable Media, Disable Guest Account, Default Domain Policy, all enabled.](https://academy.hackthebox.com/storage/modules/74/gpo_enforced.png)

Regardless of which GPO is set to enforced, if the `Default Domain Policy` GPO is enforced, it will take precedence over all GPOs at all levels.

#### Default Domain Policy Override

![Group Policy Management interface showing Computers under INLANEFREIGHT.LOCAL. Linked Group Policy Objects: Default Domain Policy, Disable Forced Restarts, Disallow LM Hash, Block Removable Media, Disable Guest Account, all enabled.](https://academy.hackthebox.com/storage/modules/74/default_gpo.png)

It is also possible to set the `Block inheritance` option on an OU. If this is specified for a particular OU, then policies higher up (such as at the domain level) will NOT be applied to this OU. If both options are set, the `No Override` option has precedence over the `Block inheritance` option. Here is a quick example. The `Computers` OU is inheriting GPOs set on the `Corp` OU in the below image.

![Group Policy Management interface showing Computers under INLANEFREIGHT.LOCAL. Linked Group Policy Objects: Logon Banner, Disable Forced Restarts, Disallow LM Hash, Block Removable Media, Disable Guest Account, Default Domain Policy, all enabled.](https://academy.hackthebox.com/storage/modules/74/inheritance.png)

If the `Block Inheritance` option is chosen, we can see that the 3 GPOs applied higher up to the `Corp` OU are no longer enforced on the `Computers` OU.

#### Block Inheritance

![Group Policy Management interface showing Computers under INLANEFREIGHT.LOCAL. Linked Group Policy Objects: Logon Banner and Disable Forced Restarts, both enabled.](https://academy.hackthebox.com/storage/modules/74/block_inheritance.png)

---

## Group Policy Refresh Frequency

When a new GPO is created, the settings are not automatically applied right away. Windows performs periodic Group Policy updates, which by default is done every 90 minutes with a randomized offset of +/- 30 minutes for users and computers. The period is only 5 minutes for domain controllers to update by default. When a new GPO is created and linked, it could take up to 2 hours (120 minutes) until the settings take effect. This random offset of +/- 30 minutes is set to avoid overwhelming domain controllers by having all clients request Group Policy from the domain controller simultaneously.

It is possible to change the default refresh interval within Group Policy itself. Furthermore, we can issue the command `gpupdate /force` to kick off the update process. This command will compare the GPOs currently applied on the machine against the domain controller and either modify or skip them depending on if they have changed since the last automatic update.

We can modify the refresh interval via Group Policy by clicking on `Computer Configuration --> Policies --> Administrative Templates --> System --> Group Policy` and selecting `Set Group Policy refresh interval for computers`. While it can be changed, it should not be set to occur too often, or it could cause network congestion leading to replication issues.

![Group Policy settings for refresh interval on computers. Options to configure interval in minutes, with a range of 0 to 44640 for policy application and 0 to 1440 for random time offset.](https://academy.hackthebox.com/storage/modules/74/comp_updates.png)
## Security Considerations of GPOs

As mentioned earlier, GPOs can be used to carry out attacks. These attacks may include adding additional rights to a user account that we control, adding a local administrator to a host, or creating an immediate scheduled task to run a malicious command such as modifying group membership, adding a new admin account, establishing a reverse shell connection, or even installing targeted malware throughout a domain. These attacks typically happen when a user has the rights required to modify a GPO that applies to an OU that contains either a user account that we control or a computer.

Below is an example of a GPO attack path identified using the [BloodHound](https://github.com/BloodHoundAD/BloodHound) tool. This example shows that the `Domain Users` group can modify the `Disconnect Idle RDP` GPO due to nested group membership. In this case, we would next look to see which OUs this GPO applies to and if we can leverage these rights to gain control over a high-value user (administrator or Domain Admin) or computer (server, DC, or critical host) and move laterally to escalate privileges within the domain.

![Network diagram showing DOMAIN USERS@INLANEFREIGHT.LOCAL as a member of AUTHENTICATED USERS@INLANEFREIGHT.LOCAL, which has connections to DISCONNECT IDLE RDP@INLANEFREIGHT.LOCAL with permissions: GenericWrite, WriteOwner, WriteDacl.](https://academy.hackthebox.com/storage/modules/74/bh_gpo.png)