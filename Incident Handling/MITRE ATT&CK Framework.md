[MITRE ATT&CK](https://attack.mitre.org/)

The MITRE ATT&CK Enterprise Matrix is a knowledge base that documents adversary behavior observed in the wild against enterprise IT environments (Windows, Linux, macOS, cloud, network, mobile, etc.). It is presented as a `matrix` where columns represent adversary goals (`tactics`), and cells are `techniques` attackers use to achieve those goals. The framework helps defenders understand, model, detect, and respond to attacker behavior in a structured way.

![The tactics and techniques representing the MITRE ATT&CK® Matrix for Enterprise.](https://academy.hackthebox.com/storage/modules/148/mitreintro.png)

#### Tactic

A tactic is a high-level adversary objective during an intrusion (the goal they want to accomplish at that stage). For Example:

- `Initial Access`.
- `Persistence`.
- `Privilege Escalation`.

#### Technique

A technique is a specific method adversaries use to achieve a tactic. Techniques describe concrete attacker behavior (tools, commands, APIs, protocols, etc.).

Techniques have IDs like [T1105 (Ingress Tool Transfer)](https://attack.mitre.org/techniques/T1105/) or [T1021 (Remote Services)](https://attack.mitre.org/techniques/T1021/). For example:

- `T1105 Ingress Tool Transfer`: Refers to the tools used by attackers to download a tool, such as `wget`, `curl`, etc., commonly OS built-in commands/tools.
- `T1021 Remote Services`: Refers to adversaries using protocols such as SSH, RDP, and SMB for lateral movement.

#### Sub-technique

Sub-techniques are children of techniques that capture a particular implementation or target. Sub-technique IDs extend the parent technique: [T1003.001 (Credential Dumping -> LSASS Memory)](https://attack.mitre.org/techniques/T1003/001/), [T1021.002 (Remote Services -> SMB/Windows Admin Shares)](https://attack.mitre.org/techniques/T1021/002/). For example:

- `T1003.001 - OS Credentials: LSASS Memory`: Refers to adversaries dumping credentials directly from the LSASS process memory when achieving the necessary privileges.
- `T1021.002 - Remote Services: SMB/Windows Admin Shares`: Refers to adversaries interacting with shares using valid credentials.

This enables precise detection, attribution, and reporting (we can say "We detected `T1003.001` — LSASS memory dumping" instead of just `T1003`).

### Pyramid of Pain

![Pyramid of pain graphic mapping indicator types to defender difficulty. From bottom to top: Hash Values (Trivial), IP Addresses (Easy), Domain Names (Simple), Network/Host Artifacts (Annoying), Tools (Challenging), TTPs—ATT&CK (Tough). Header shows MITRE ATT&CK tactics across the top (Reconnaissance through Impact).](https://academy.hackthebox.com/storage/modules/148/ir_mitre.png)
