# From Cisco:
>Spanning Tree Protocol (STP) is a Layer 2 protocol that runs on bridges and switches. The specification for STP is IEEE 802.1D. The main purpose of STP is to ensure that you do not create loops when you have redundant paths in your network. Loops are deadly to a network.
# From Wikipedia:
>The Spanning Tree Protocol (STP) is a network protocol that builds a loop-free logical topology for Ethernet networks. The basic function of STP is to prevent bridge loops and the broadcast radiation that results from them. Spanning tree also allows a network design to include backup links providing fault tolerance if an active link fails.

STP is an old protocol. Radia Perlman invented it. Right now, STP, RSTP and MSTP have been merged into the gargantuam (wild guess) standard, IEEE 802.1Q (cute name: Dot1q). This is somehow tied into VLANs in ways I don't care about.\
Also note, that situations which require any of STP or its derivatives, usually come around with a bunch of switches connected to give redundancy. We just don't want our packets taking infinite roundtrips inside the network and never poking their heads out of their proverbial arses.

<table class="wikitable" width="400px">
    <caption>802.1Q tag format
    </caption>
        <tbody>
            <tr>
                <th width="50%">16 bits</th>
                <th width="9.375%">3 bits</th>
                <th width="3.125%">1 bit</th>
                <th width="37.5%">12 bits</th>
            </tr>
            <tr>
                <td rowspan="2" align="center">TPID</td>
                <td colspan="3" align="center">TCI</td>
            </tr>
            <tr>
                <td align="center">PCP</td>
                <td align="center">DEI</td>
                <td align="center">VID</td>
            </tr>
        </tbody>
</table>

<br>

The aim of STP is to figure out the best route from a number of redundant links, and only pass packets in that best. Btw, the best route is measured by the total path cost of the combinations of paths to the root bridge. This preferred link is used for all Ethernet frames unless it fails, in which case a non-preferred redundant link is enabled. When implemented in a network, STP designates one layer-2 switch as root bridge. All switches then select their best connection towards the root bridge for forwarding and block other redundant links. All switches constantly communicate with their neighbors in the LAN using Bridge Protocol Data Units (BPDUs).

<table class="wikitable floatright">
    <caption>Path cost for different port speed and STP variation</caption>
    <tbody>
        <tr>
            <th>Data rate</th>
            <th>STP cost</th>
            <th>RSTP cost</th>
        </tr>
        <tr>
            <td><b>(Link bandwidth)</b></td>
            <td><b>(802.1D-1998)</b></td>
            <td><b>(802.1W-2004 default value)</b></td>
        </tr>
        <tr>
            <td>4&nbsp;Mbit/s</td>
            <td>250</td>
            <td>5,000,000</td>
        </tr>
        <tr>
            <td>10&nbsp;Mbit/s</td>
            <td>100</td>
            <td>2,000,000</td>
        </tr>
        <tr>
            <td>16&nbsp;Mbit/s</td>
            <td>62</td>
            <td>1,250,000</td>
        </tr>
        <tr>
            <td>100&nbsp;Mbit/s</td>
            <td>19</td>
            <td>200,000</td>
        </tr>
        <tr>
            <td>1&nbsp;Gbit/s</td>
            <td>4</td>
            <td>20,000</td>
        </tr>
        <tr>
            <td>2&nbsp;Gbit/s</td>
            <td>3</td>
            <td>10,000</td>
        </tr>
        <tr>
            <td>10&nbsp;Gbit/s</td>
            <td>2</td>
            <td>2,000</td>
        </tr>
        <tr>
            <td>100&nbsp;Gbit/s</td>
            <td>N/A</td>
            <td>200</td>
        </tr>
        <tr>
            <td>1&nbsp;Tbit/s</td>
            <td>N/A</td>
            <td>20</td>
        </tr>   
    </tbody>
</table>
<br>

# Root bridge:
*From wikipedia:*\
The root bridge of the spanning tree is the bridge with the smallest (lowest) bridge ID. Each bridge has a configurable priority number and a MAC address; the bridge ID is the concatenation of the bridge priority and the MAC address. For example, the ID of a bridge with priority `32768` and MAC `0200.0000.1111` is `32768.0200.0000.1111`. The bridge priority default is `32768` and can only be configured in multiples of `4096`. When comparing two bridge IDs, the priority portions are compared first and the **MAC addresses are compared only if the priorities are equal**. The switch with the lowest priority of all the switches will be the root; if there is a tie, then the switch with the lowest priority and lowest MAC address will be the root. For example, if switches A (MAC = `0200.0000.1111`) and B (MAC = `0200.0000.2222`) both have a priority of `32768` then switch A will be selected as the root bridge. If the network administrators would like switch B to become the root bridge, they must set its priority to be less than `32768`.

# Electing the root bridge:
Quoting from the CCNA 2 pages:
>During STA and STP functions, switches use Bridge Protocol Data Units (BPDUs) to share information about themselves and their connections. **BPDU**s are used to elect the root bridge, root ports, designated ports, and alternate ports. Each **BPDU** contains a bridge ID (BID) that identifies which switch sent the **BPDU**. The BID is involved in making many of the STA decisions including root bridge and port roles. As shown in the figure, the BID contains a priority value, an extended system ID, and the MAC address of the switch. The lowest BID value is determined by the combination of these three fields.

![The BID](https://upload.wikimedia.org/wikipedia/commons/a/a9/Spanning_tree_protocol_at_work_2.svg)
##### *The BID includes the Bridge Priority, the Extended System ID, and the MAC Address of the switch.*

>The graphic shows three boxes, each representing a component of the bridge ID. From left to right the first box is Bridge Priority which is 4 bits in length, the second box is Extended System ID which is 12 bits in length, and the third box is the MAC address which is 48 bits in length. Text to the right of the boxes reads Bridge ID with the Extended System ID. Text at the bottom of the graphic reads The BID includes the Bridge Priority, the Extended System ID, and the MAC address of the switch.

From the [paragraph on root bridges](#Root-bridge:), the default priority is `32768`. Thus, switch with the lowest numeric MAC address becomes the root bridge. BPDUs are sent out by each switch to every other switch, and after the exchanges are done, all switches agree on the Root Bridge.

>The root port is the port closest to the root bridge in terms of overall cost (best path) to the root bridge. This overall cost is known as the internal root path cost. The internal root path cost is equal to the sum of all the port costs along the path to the root bridge. Paths with the lowest cost become preferred, and all other redundant paths are blocked.

>In Per-VLAN Spanning Tree (PVST) versions of STP, there is a root bridge elected for each spanning tree instance. This makes it possible to have different root bridges for different sets of VLANs. STP operates a separate instance of STP for each individual VLAN. If all ports on all switches are members of VLAN 1, then there is only one spanning tree instance.

# References:
[Wikipedia's article on STP](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol)\
[Dummies intro to STP](https://www.dummies.com/programming/networking/cisco/spanning-tree-protocol-stp-introduction/)\
[Cisco docs on understanding and configuring STP](https://www.cisco.com/c/en/us/support/docs/lan-switching/spanning-tree-protocol/5234-5.html)

<br><br><br><br><br>

# This fine lady:
![This fine lady](https://upload.wikimedia.org/wikipedia/commons/a/af/Radia_Perlman_2009.jpg)


# The problem here, is that there is no TTL in the Layer 2.

Thus, there is nothing preventing a continuous broadcast of packets, especially things like `ARP`.\
That's why you need `STP`, because it will take care of the redundant links and also reduce the broadcast domain.

# Commands



# Resources

- https://www.ciscopress.com/articles/article.asp?p=2832407&seqNum=6
- https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst6500/ios/12-2SX/configuration/guide/book/stp_enha.html
- https://www.cisco.com/c/en/us/support/docs/smb/switches/cisco-small-business-300-series-managed-switches/smb5760-configure-stp-settings-on-a-switch-through-the-cli.html
- https://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r4-0/addr_serv/command/reference/ir40asrbook_chapter2.html
- https://community.cisco.com/t5/switching/show-arp-vs-show-mac-address-table/td-p/1584636
