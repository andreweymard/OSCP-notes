## Subdomain Enumeration
``` bash
user@parrot:~/Desktop$ while IFS= read -r subdomain; do host "$subdomain" | awk -v original="$subdomain" '/has address/ && /domain.com.au/ {print original, $1, $4}'; done < subdomainlist

api-prod.domain.com.au api-prod.domain.com.au 4.237.18.124
api-uat.domain.com.au api-uat.domain.com.au 20.58.186.140
applications.domain.com.au cname.vercel-dns.com 66.33.60.194
applications.domain.com.au cname.vercel-dns.com 66.33.60.193
apply.domain.com.au proxy-ssl-geo-2.webflow.com 3.109.243.18
apply.domain.com.au proxy-ssl-geo-2.webflow.com 13.203.125.58
apply.domain.com.au proxy-ssl-geo-2.webflow.com 13.233.175.166
```

<mark>subdomainlist</mark> contains a list of subdomains enumerated from the below command
``` bash
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u
```

## Shodan IP parsing

Redirect the output from above into a file that only contains the IP addresses, Be casreful as these IP addresses are also of 3rd party hosting providers and they may not look too keen on your fuzzing. Besides filtering by domain.com.au will help filter out those 3rd party hosters.

``` bash
user@parrot:~/Desktop$ while IFS= read -r ipaddresses; do shodan host "$ipaddresses"; done < ipaddresses 
Error: Access denied (403 Forbidden)
Error: Access denied (403 Forbidden)
75.2.70.75
Hostnames:               aacb0a264e514dd48.awsglobalaccelerator.com;signaturemaids.com
City:                    Seattle
Country:                 United States
Organization:            Amazon.com, Inc.
Updated:                 2025-10-10T20:58:40.805157
Number of open ports:    2

Ports:
     80/tcp nginx 
    443/tcp  
	|-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
```

## Curl for ASN of IP addresses
``` bash
user@parrot:~/Desktop$ while IFS= read -r ips; do curl https://api.ipinfo.io/lite/"$ips"?token=$TOKEN; done < ipaddresses 
{
  "ip": "4.237.18.124",
  "city": "Sydney",
  "region": "New South Wales",
  "country": "AU",
  "loc": "-33.8678,151.2073",
  "org": "AS8075 Microsoft Corporation",
  "postal": "1001",
  "timezone": "Australia/Sydney",
  "readme": "https://ipinfo.io/missingauth"
}{
  "ip": "20.58.186.140",
  "city": "Sydney",
  "region": "New South Wales",
  "country": "AU",
  "loc": "-33.8678,151.2073",
  "org": "AS8075 Microsoft Corporation",
  "postal": "1001",
  "timezone": "Australia/Sydney",
  "readme": "https://ipinfo.io/missingauth"
}{
  "ip": "75.2.70.75",
  "hostname": "aacb0a264e514dd48.awsglobalaccelerator.com",
  "city": "Seattle",
  "region": "Washington",
  "country": "US",
  "loc": "47.5413,-122.3129",
  "org": "AS16509 Amazon.com, Inc.",
  "postal": "98108",
  "timezone": "America/Los_Angeles",
  "readme": "https://ipinfo.io/missingauth",
  "anycast": true
}{
  "ip": "99.83.190.102",
  "hostname": "aacb0a264e514dd48.awsglobalaccelerator.com",
  "city": "Seattle",
  "region": "Washington",
  "country": "US",
  "loc": "47.5413,-122.3129",
  "org": "AS16509 Amazon.com, Inc.",
  "postal": "98108",
  "timezone": "America/Los_Angeles",
  "readme": "https://ipinfo.io/missingauth",
  "anycast": true
}{
  "ip": "76.223.36.225",
  "hostname": "a5671e0bec5554d9f.awsglobalaccelerator.com",
  "city": "Seattle",
  "region": "Washington",
  "country": "US",
  "loc": "47.5413,-122.3129",
  "org": "AS16509 Amazon.com, Inc.",
  "postal": "98108",
  "timezone": "America/Los_Angeles",
  "readme": "https://ipinfo.io/missingauth",
  "anycast": true
}{
  "ip": "13.248.141.80",
  "hostname": "a5671e0bec5554d9f.awsglobalaccelerator.com",
  "city": "Seattle",
  "region": "Washington",
  "country": "US",
  "loc": "47.6339,-122.3476",
  "org": "AS16509 Amazon.com, Inc.",
  "postal": "98109",
  "timezone": "America/Los_Angeles",
  "readme": "https://ipinfo.io/missingauth",
  "anycast": true
}{
  "ip": "20.5.104.151",
  "city": "Sydney",
  "region": "New South Wales",
  "country": "AU",
  "loc": "-33.8678,151.2073",
  "org": "AS8075 Microsoft Corporation",
  "postal": "1001",
  "timezone": "Australia/Sydney",
  "readme": "https://ipinfo.io/missingauth"
}user@parrot:~/Desktop$ 



```

## DNS enumeration

``` bash
user@parrot:~/Desktop$ dig any aeymard.com

;; global options: +cmd
;; Got answer:
;; ->>HEADER\<<- opcode: QUERY, status: NOERROR, id: 23754
;; flags: qr rd ra; QUERY: 1, ANSWER: 6, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;aeymard.com.			IN	ANY

;; ANSWER SECTION:
aeymard.com.		3600	IN	HINFO	"RFC8482" ""
aeymard.com.		84861	IN	DS	2371 13 2 3F002EF8F3E73701942A986915DBCEC78B166713A3FC3EA8D02D2063 0151F1BC
aeymard.com.		171261	IN	NS	val.ns.cloudflare.com.
aeymard.com.		171261	IN	NS	elijah.ns.cloudflare.com.
aeymard.com.		3600	IN	RRSIG	HINFO 13 2 3600 20251012055343 20251010035343 34505 aeymard.com. 2oohJzuhRQHQDq/3k/Aa9aN3hzHuSm3dj3KEkC3pJ4E4+njjpYvd+EZP j9k8qlOCG4LinSly6mkhMrUaOkCdug==
aeymard.com.		84861	IN	RRSIG	DS 13 2 86400 20251015015155 20251008004155 20545 com. eUujITs12HGhR3+QxHloqmit7o9Qqv5C7hRGRZED8SRHDWDD/si9CRC5 1VJmNb921jK+1ue03igEE1UU7pLLew==

;; Query time: 53 msec
;; SERVER: 10.0.2.3#53(10.0.2.3) (TCP)
;; WHEN: Sat Oct 11 15:53:43 AEDT 2025
;; MSG SIZE  rcvd: 368

```

