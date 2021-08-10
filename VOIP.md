# VOIP in Packet Tracer.

## Snapshot of the network.

![The pic](../../02_Assets/01_Networking/packet_tracer/VOIP/VOIP_draft.JPG)

## Configuration of the router

```
Router>enable
Router#configure terminal
Router(config)#interface gigabitEthernet 0/0/1
Router(config-if)#ip address 192.168.10.1 255.255.255.0
Router(config-if)#no shutdown
Router(config-if)#exit

Router(config)#ip dhcp pool VOICE
Router(dhcp-config)#network 192.168.10.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.10.1
Router(dhcp-config)#option 150 ip 192.168.10.1
```

# Edit:

Didn't do much. Will have to get it done ASAP, preferably by tomorrow. It's a great idea for me (imagine a VOIP phone in my to-be virtual apartment! No picking up my android phone anymore, I can switch it off).\
The resources I used:

[intenseschool TUT](http://resources.intenseschool.com/voip-in-packet-tracer-basic-labs/)\
[packettracernetwork TUT](https://www.packettracernetwork.com/tutorials/voipconfiguration.html)\
[Cisco DOCS on VOIP](https://www.cisco.com/c/en/us/support/docs/voice/h323/25100-config-troubleshoot-its.html)