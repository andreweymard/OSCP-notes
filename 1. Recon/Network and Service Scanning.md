## Understanding the Assigned Scope

Before initiating a scan, it is critical to define and understand the scope of the assessment. The scope typically includes a range of IP addresses, domains, or systems that the client has authorized for testing. For example, a scope might consist of a single subnet, such as 10.129.12.0/24, which encompasses all IP addresses from 10.129.12.0 to 10.129.12.255. Understanding the scope ensures that testing remains within legal and ethical boundaries and focuses on the systems relevant to the client’s objectives.

The primary goal of network scanning is to identify active hosts within the scope and determine the services they expose. Active hosts are systems that respond to network probes, indicating they are online and potentially part of the target environment. Services, on the other hand, are applications or protocols running on specific ports, such as HTTP (port 80), SSH (port 22), or SMB (port 445). By mapping these services, we can infer the purpose of each system, its role in the network, and potential attack vectors.

