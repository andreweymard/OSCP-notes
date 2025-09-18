## VLAN Identification

Standard `802.3` `Ethernet` frames do not contain `VLAN` information; therefore, switches and other `VLAN`-enabled devices need a mechanism to keep track of all the `VLAN` information associated with a packet while traversing `VLAN`-enabled devices. Two main `trunking methods` are utilized to achieve this, `ISL` and `IEEE 802.1Q`.

#### Inter-Switch Link (ISL)

`Inter-Switch Link` (`ISL`) is a Cisco-proprietary protocol used for trunking between `VLAN`-enabled devices. Although `ISL` is one of the first `trunking methods` (predating `802.1Q`), it is deprecated and not as widely used in modern Cisco switches (and routers). Instead, most only support the widely adopted `802.1Q`. `ISL` encapsulated the entire `Ethernet` frame, including the original `Ethernet` header and the `VLAN` tag, adding its 26-byte header and 4-byte trailer.

#### IEEE 802.1Q

To ensure interoperability of `VLAN` technologies from the various network-equipments vendors, the `Institute of Electrical and Electronics Engineers` (`IEEE`) developed the [802.1Q](https://ieeexplore.ieee.org/document/10004498) specification in 1998. The `IEEE 802` committee had to change the `802.3` `Ethernet` frame format by adding a pair of 2-byte fields, `TPID` and `TCI` (which consists of three subfields, `PCP`, `DEI`, and `VID`), resulting in a `VLAN`-compliant `802.1Q` `Ethernet` frame.

![Diagram showing conversion of a 802.3 Ethernet frame to a 802.1Q Ethernet frame by inserting a 802.1Q header between the source address and length fields. The 802.1Q header includes TPID, PCP, DEI, and VID.](https://academy.hackthebox.com/storage/modules/34/8023_Legacy_8021Q_Ethernet_Frames.png)

`Tag protocol identifier` (`TPID`) is a 16-bit field always set to `0x8100` to identify the `Ethernet` frame as an `802.1Q`-tagged frame. `Tag Control Information` (`TCI`) is a 16-bit field containing `Priority code point` (`PCP`), `Drop eligible indicator` (`DEI`) (previously known as `Canonical format indicator` (`CFI`)), and `VLAN identifier` (`VID`). The main field concerning `VLANs` is `VID`, occupying the low-order 12-bits of `TCI`. Since it is 12 bits, it allows 2^12 - 2 = 4096 (remember, `0` and `4095` are reserved) `VLAN` IDs. Therefore, an `802.1Q`-tagged frame can contain information for 4094 `VLANs`; the practice of inserting multiple `802.1Q` tags within a single packet is known as `Double Tagging`, introduced by [802.1ad](https://standards.ieee.org/ieee/802.1Q/10323/). `VLAN tagging` is the process of inserting `VLAN` information into an `802.1Q` `Ethernet header`, while `VLAN untagging` is the process of removing the `VLAN` information from an `802.1Q`-tagged `Ethernet` frame and forwarding the packet to the destined ports.

## VLAN hopping

We can use tools such as [Yersinia](https://linux.die.net/man/8/yersinia) to perform `VLAN hopping` attacks:

![Yersinia interface showing protocol attack options with 'enabling trunking' selected for Dynamic Trunking Protocol (DTP).](https://academy.hackthebox.com/storage/modules/34/Yersinia_DTP_Attack.png)

#### Double-tagging VLAN Hopping

The `double-tagging VLAN hopping attack` is an increasingly more sophisticated attack against `VLANs`. Although `VLAN double-tagging` is a legitimate practice that entities such as `Internet Service Providers` (`ISPs`) utilize (they can use their `VLANs` internally while carrying traffic from clients that are already `VLAN tagged`), adversaries can also attempt to abuse it. In a `double-tagging VLAN hopping attack`, an adversary embeds a hidden `802.1Q` tag inside an `Ethernet` frame that already has an `802.1Q` tag, allowing the frame to go to a different `VLAN`, which the original `802.1Q` tag did not specify.

An adversary can carry out this attack following three steps. Bare in mind that this attack only works if the adversary is connected to a port residing in the same `VLAN` as the `native VLAN` of the trunk port:

1. The adversary sends a `double-tagged 802.1Q` `Ethernet` frame to the switch with the outer header having the `VLAN` ID of the adversary, which is the same as the native `VLAN` of the trunk port. Assume that the native `VLAN` is `VLAN 10` and that `VLAN 30` is the `VLAN` the adversary wants to reach, where the victim resides.
2. The outer 4-byte `802.1Q` tag arrives on the switch, and it is seen to be destined for `VLAN 10`, the native `VLAN`. After removing the `VLAN 10` tag, the frame is forwarded on all `VLAN 10` ports. On the trunk port, the `VLAN 10` tag is stripped (removed), and the packet is not re-tagged because it is part of the native `VLAN`. However, the `VLAN 30` tag is still intact (not stripped), and the first switch has not inspected it.
3. Subsequently, the switch will look only at the inner `802.1Q` tag that the adversary sent, and it decides that the frame must be forwarded for `VLAN 30`, which is the adversary's chosen `VLAN`. Now, the second switch will either send the frame to the victim port directly or flood it, depending on whether there is an existing MAC address table entry for the victim host.

[Scapy](https://scapy.readthedocs.io/en/latest/usage.html#vlan-hopping) allows carrying out the `double-tagging VLAN hopping attack`, in addition to `Yersinia`:

![Yersinia interface showing protocol attack options with 'sending 802.1Q double enc. packet' selected.](https://academy.hackthebox.com/storage/modules/34/Yersinia_Double_Tagging_VLAN_Hopping_Attack.png)