* SAM grants rights to a network to execute specific processes.
* The access rights themselves are managed by Access Control Entries (ACE) in Access Control Lists (ACL).
* The permissions to access a securable object are given by the security descriptor, classified into two types of ACLs: the `Discretionary Access Control List (DACL)` or `System Access Control List (SACL)`.
* Every thread and process started or initiated by a user goes through an authorization process.
* An integral part of this process is access tokens, validated by the Local Security Authority (LSA).