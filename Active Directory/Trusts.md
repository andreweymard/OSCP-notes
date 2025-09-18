| **Trust Type** | **Description**                                                                                                                                                        |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Parent-child` | Domains within the same forest. The child domain has a two-way transitive trust with the parent domain.                                                                |
| `Cross-link`   | a trust between child domains to speed up authentication.                                                                                                              |
| `External`     | A non-transitive trust between two separate domains in separate forests which are not already joined by a forest trust. This type of trust utilizes SID filtering.     |
| `Tree-root`    | a two-way transitive trust between a forest root domain and a new tree root domain. They are created by design when you set up a new tree root domain within a forest. |
| `Forest`       | a transitive trust between two forest root domains.                                                                                                                    |
![Diagram of Inlanefreight Forest showing trust types: Parent-child, Cross-link, External, Tree-root, and Forest. Domains include inlanefreight, corp.inlanefreight, wh.corp.inlanefreight, dev.inlanefreight, freightlogistics, and shippinglanes.](https://academy.hackthebox.com/storage/modules/74/trusts-diagram.png)

