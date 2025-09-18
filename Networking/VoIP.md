The most common VoIP ports are `TCP/5060` and `TCP/5061`, which are used for the [Session Initiation Protocol](https://en.wikipedia.org/wiki/Session_Initiation_Protocol) (SIP). However, the port `TCP/1720` may also be used by some VoIP systems for the [H.323 protocol](https://en.wikipedia.org/wiki/H.323), a set of standards for multimedia communication over packet-based networks. Still, SIP is more widely used than H.323 in VoIP systems.

|**Method**|**Description**|
|---|---|
|`INVITE`|Initiates a session or invites another endpoint to participate.|
|`ACK`|Confirms the receipt of an INVITE request.|
|`BYE`|Terminate a session.|
|`CANCEL`|Cancels a pending INVITE request.|
|`REGISTER`|Registers a SIP user agent (UA) with a SIP server.|
|`OPTIONS`|Requests information about the capabilities of a SIP server or user agent, such as the types of media it supports.|
