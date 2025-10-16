## Scanning Options

| Nmap Option          | Description                                                               |
| :------------------- | :------------------------------------------------------------------------ |
| `10.10.10.0/24`      | Target network range.                                                     |
| `-sn`                | Disables port scanning (Ping Scan).                                       |
| `-Pn`                | Disables host discovery (treats all hosts as online).                     |
| `-n`                 | Disables DNS Resolution.                                                  |
| `-PE`                | Performs the ping scan by using ICMP Echo Requests against the target.    |
| `--packet-trace`     | Shows all packets sent and received.                                      |
| `--reason`           | Displays the reason for a specific result (e.g., host up, port state).    |
| `--disable-arp-ping` | Disables ARP Ping Requests.                                               |
| `--top-ports=<num>`  | Scans the specified top ports that have been defined as most frequent.    |
| `-p-`                | Scans all 65,535 TCP ports.                                               |
| `-p22-110`           | Scans all ports between 22 and 110.                                       |
| `-p22,25`            | Scans only the specified ports 22 and 25.                                 |
| `-F`                 | Scans the top 100 most common ports (Fast Scan).                          |
| `-sS`                | Performs a TCP SYN-Scan (Stealth Scan).                                   |
| `-sA`                | Performs a TCP ACK-Scan (used for firewall discovery).                    |
| `-sU`                | Performs a UDP Scan.                                                      |
| `-sV`                | Scans the discovered services for their versions.                         |
| `-sC`                | Performs a Script Scan with scripts that are categorized as "default".    |
| `--script <script>`  | Performs a Script Scan by using the specified scripts.                    |
| `-O`                 | Performs an OS Detection Scan to determine the OS of the target.          |
| `-A`                 | Enables OS detection, version detection, script scanning, and traceroute. |
| `-D RND:5`           | Sets the number of random Decoys that will be used to scan the target.    |
| `-e <interface>`     | Specifies the network interface that is used for the scan.                |
| `-S <ip_address>`    | Specifies the source IP address for the scan (IP Spoofing).               |
| `-g <port>`          | Specifies the source port for the scan.                                   |
| `--dns-server <ns>`  | DNS resolution is performed by using a specified name server.             |

***

## Output Options

| Nmap Option      | Description                                                                                          |
| :--------------- | :--------------------------------------------------------------------------------------------------- |
| `-oA <filename>` | Stores the results in all available formats (`.nmap`, `.gnmap`, `.xml`) with the specified filename. |
| `-oN <filename>` | Stores the results in normal format with the specified filename.                                     |
| `-oG <filename>` | Stores the results in "grepable" format with the specified filename.                                 |
| `-oX <filename>` | Stores the results in XML format with the specified filename.                                        |

***

## Performance Options

| Nmap Option | Description |
| :--- | :--- |
| `--max-retries <num>` | Sets the number of retries for scans of specific ports. |
| `--stats-every=5s` | Displays the scan's status every 5 seconds. |
| `-v` / `-vv` | Displays verbose / very verbose output during the scan. |
| `--initial-rtt-timeout <time>` | Sets the specified time value (e.g., `50ms`) as initial RTT timeout. |
| `--max-rtt-timeout <time>` | Sets the specified time value (e.g., `100ms`) as maximum RTT timeout. |
| `--min-rate <num>` | Sets the number of packets that will be sent per second (e.g., `300`). |
| `-T <0-5>` | Specifies the timing template (0=paranoid, 1=sneaky, 2=polite, 3=normal, 4=aggressive, 5=insane). |