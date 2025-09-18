#### IP header

The header of an IP packet contains several fields that have important information.

|**Field**|**Description**|
|---|---|
|`Version`|Indicates which version of the IP protocol is being used|
|`Internet Header Length`|Indicates the size of the header in 32-bit words|
|`Class of Service`|Means how important the transmission of the data is|
|`Total length`|Specifies the total length of the packet in bytes|
|`Identification (ID)`|Is used to identify fragments of the packet when fragmented into smaller parts|
|`Flags`|Used to indicate fragmentation|
|`Fragment Offset`|Indicates where the current fragment is placed in the packet|
|`Time to Live`|Specifies how long the packet may remain on the network|
|`Protocol`|Specifies which protocol is used to transmit the data, such as TCP or UDP|
|`Checksum`|Is used to detect errors in the header|
|`Source/Destination`|Indicate where the packet was sent from and where it is being sent to|
|`Options`|Contain optional information for routing|
|`Padding`|Pads the packet to a full word length|
#### IP Record-Route Field

The `Record-Route field` in the IP header also records the route to a destination device. When the destination device sends back the `ICMP Echo Reply` packet, the IP addresses of all devices that pass through the packet are listed in the `Record-Route field` of the IP header. This happens when we use the following command, for example:

TCP/UDP Connections

```shell-session
astrocat@htb[/htb]$ ping -c 1 -R 10.129.143.158

PING 10.129.143.158 (10.129.143.158) 56(124) bytes of data.
64 bytes from 10.129.143.158: icmp_seq=1 ttl=63 time=11.7 ms
RR: 10.10.14.38
        10.129.0.1
        10.129.143.158
        10.129.143.158
        10.10.14.1
        10.10.14.38


--- 10.129.143.158 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 11.688/11.688/11.688/0.000 ms
```

The output indicates that a `ping` request was sent and a response was received from the destination device and also shows the `Record-Route field` in the IP header of the `ICMP Echo Request` packet. The Record Route field contains the IP addresses of all devices that passed through the `ICMP Echo Request` packet on the way to the destination device. In this case, the `Record-Route field` contains the IP addresses:

||||
|---|---|---|
|10.10.14.38|10.129.0.1|10.129.143.158|
|10.129.143.158|10.10.14.1|10.10.14.38|