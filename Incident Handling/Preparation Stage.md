In the `Preparation` stage, we have two separate objectives. The first is the establishment of incident handling capability within the organization. The second is the ability to protect against and prevent IT security incidents by implementing appropriate protective measures. Such measures include endpoint and server hardening, Active Directory tiering, Multi-Factor Authentication, privileged access management, and so on.

![Slide titled “Incident Handling Preparation Prerequisites” listing five items: Incident handling capability within the organization, Protect against & prevent IT security incidents, Clear policies, Documentation, and Tools (software & hardware).](https://academy.hackthebox.com/storage/modules/148/ir_preparation.png)
## Clear Policies & Documentation

Some of the written policies and documentation should contain an up-to-date version of the following information:

- Contact information and roles of the incident handling team members.
- Contact information for the legal and compliance department, management team, IT support, communications and media relations department, law enforcement, internet service providers, facility management, and external incident response team.
- Incident response policy, plan, and procedures.
- Incident information sharing policy and procedures.
- Baselines of systems and networks, out of a golden image and a clean state environment.
- Network diagrams.
- Organization-wide asset management database.
- User accounts with excessive privileges that can be used on-demand by the team when necessary (also for business-critical systems, which are handled with the skills needed to administer that specific system). These user accounts are normally enabled when an incident is confirmed during the initial investigation and then disabled once it is over. A mandatory password reset is also performed when disabling the users.
- Ability to acquire hardware, software, or an external resource without a complete procurement process (urgent purchase of up to a certain amount). The last thing you need during an incident is to wait for weeks for the approval of a $500 tool.
- Forensic/Investigative cheat sheets.

## Tools (Software & Hardware)

Moving forward, we also need to ensure that we have the right tools to perform the job. These include, but are not limited to:

- An additional laptop or a forensic workstation for each incident handling team member to preserve disk images and log files, perform data analysis, and investigate without any restrictions (we know malware will be tested here, so tools such as antivirus should be disabled). These devices should be handled appropriately and not in a way that introduces risks to the organization.
- Digital forensic image acquisition and analysis tools.
- Memory capture and analysis tools.
- Live response capture and analysis tools.
- Log analysis tools.
- Network capture and analysis tools.
- Network cables and switches.
- Write blockers.
- Hard drives for forensic imaging.
- Power cables.
- Screwdrivers, tweezers, and other relevant tools to repair or disassemble hardware devices if needed.
- Indicator of Compromise (IOC) creator and the ability to search for IOCs across the organization.
- Chain of custody forms.
- Encryption software.
- Ticket tracking system.
- Secure facility for storage and investigation.
- Incident handling system independent of your organization's infrastructure.

Many of the tools mentioned above will be part of what is known as a <mark>jump bag</mark>

## Endpoint Hardening (& EDR)
There are a few widely recognized endpoint hardening standards now, with CIS and Microsoft baselines being the most popular, and these should really be the building blocks for our organization's hardening baselines. Some highly important actions (that actually work) to note and do something about are:

- Disable LLMNR/NetBIOS.
- Implement LAPS and remove administrative privileges from regular users.
- Disable or configure PowerShell in "ConstrainedLanguage" mode.
- Enable Attack Surface Reduction (ASR) rules if using Microsoft Defender.
- Implement whitelisting. We know this is nearly impossible to implement. Consider at least blocking execution from user-writable folders (Downloads, Desktop, AppData, etc.). These are the locations where exploits and malicious payloads will initially find themselves. Remember to also block script types such as .hta, .vbs, .cmd, .bat, .js, and similar. We need to pay attention to [LOLBin](https://lolbas-project.github.io) files while implementing whitelisting. Do not overlook them; they are really used in the wild as initial access to bypass whitelisting.
- Utilize host-based firewalls. As a bare minimum, block workstation-to-workstation communication and block outbound traffic to LOLBins.
- Deploy an EDR product. At this point in time, [AMSI](https://learn.microsoft.com/en-us/windows/win32/amsi/how-amsi-helps) provides great visibility into obfuscated scripts for antimalware products to inspect the content before it gets executed. It is highly recommended that we only choose products that integrate with AMSI.