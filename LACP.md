# Sample Steps for configuring Etherchannel for an L2 switch using LACP:
```
S1(config)#interface range [interface-type interface-range]
S1(config-if-range)#shutdown
S1(config-if-range)#channel-group <id> mode ?

active     Enable LACP unconditionally  
auto       Enable PAgP only if a PAgP device is   detected  
desirable  Enable PAgP unconditionally  
on         Enable Etherchannel only  
passive    Enable LACP only if a LACP device is detected 

S1(config-if-range)#channel-group <id> mode <some mode>
S1(config-if-range)#no shutdown
S1(config-if-range)#interface port-channel <id>
S1(config-if)#switchport mode trunk
S1(config-if)#switchport trunk allowed vlan <multiple VLANs should be comma separated but without spaces>
```

Notes:
- Channel-group ids range from <1-6>
- The `shutdown` and `no shutdown` commands are a good practice.
- If a port-channel is disabled by STP, and the solution is to make that switch the root bridge: `S1(config)#spanning-tree vlan 1 root primary`.
- Configure the ports as trunked ports. Access ports and DTP (default settings) will cause misconfiguration and failure.
- If you get a message like `Command rejected: An interface whose trunk encapsulation is "Auto" can not be configured to "trunk" mode.` while disabling DTP, follow these commands:
    ```
    Switch(config-if)# switchport trunk encapsulation dot1q
    Switch(config-if)# switchport mode trunk
    Switch(config-if)# switchport nonegotiate
    ```
- >It is easy to confuse PAgP or LACP with DTP, because they are all protocols used to automate behavior on trunk links. PAgP and LACP are used for link aggregation (EtherChannel). DTP is used for automating the creation of trunk links. When an EtherChannel trunk is configured, typically EtherChannel (PAgP or LACP) is configured first and then DTP.

Troubleshooting:
- `show interfaces port-channel 1` or whichever channel you've used.
- `show etherchannel summary`

    ```
    S1#show etherchannel summary 
    Flags:  D - down        P - in port-channel
            I - stand-alone s - suspended
            H - Hot-standby (LACP only)
            R - Layer3      S - Layer2
            U - in use      f - failed to allocate aggregator
            u - unsuitable for bundling
            w - waiting to be aggregated
            d - default port


    Number of channel-groups in use: 1
    Number of aggregators:           1

    Group  Port-channel  Protocol    Ports
    ------+-------------+-----------+----------------------------------------------

    1      Po1(SU)           PAgP   Fa0/21(P) Fa0/22(P) 
    ```
- `show etherchannel port-channel`
- `show interfaces f0/1 etherchannel`
- Set every port-channel (Po) to `trunk`, set their mode to `active` (use the `no` at the beginning of whatever shit in there and reverse it), and set every individual interface to `trunk`. Doing this will almost guarantee a safe, working network.
- >VLAN match - All interfaces in the EtherChannel bundle must be assigned to the same VLAN or be configured as a trunk.
- >Range of VLANs - An EtherChannel supports the same allowed range of VLANs on all the interfaces in a trunking EtherChannel.
