# Contents:
- [Basic VLAN config](#Here's-how-to-configure-a-VLAN:)
- [Switch ports](#Adding-switch-ports-to-VLAN:)
- [Trunk ports](#Trunk-port:)
- [DTP](#DTP:)
- [Types of Inter-VLAN routing](#There-are-3-main-types-of-Inter-VLAN:)
    - [The Legacy Method](#Legacy-method:)
    - [Router on a stick](#Router-on-a-stick:)
    - [Inter-VLAN routing on an L3 switch](#Inter-VLAN-routing-on-an-L3-switch:)
- [VTP](#VTP:)
    - [Configuring the server](#VTP-server:)
    - [Configuring the client](#VTP-client:)
    - [Staying transparent:](#Staying-transparent:)
    - [For reference and checking configuration:](#For-reference-and-checking-configuration:)
- [Troubleshooting](#Troubleshooting:)
- [A few resources](#A-few-resources:)


# Here's how to configure a VLAN:
```
switch>enable
switch#configure terminal
switch(config)#hostname S1
S1(config)#interface vlan 99
S1(config-if)#ip address 192.168.1.2 255.255.255.0
S1(config-if)#no shutdown
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.1.1
S1(config)#end
## Same for any other VLANs you'd want to create. Change IP addresses and default gateways accordingly.
```

# Adding switch ports to VLAN:
```
S1>enable
S1#configure terminal
S1(config)#interface fastEthernet 0/1
S1(config-if)#switchport mode access 
S1(config-if)#switchport access vlan 99
## Same for any other ports you'd want to add.
```

# Trunk port:
<u><i>Situation</i></u>: 2 switches, and 3 VLANs each. We need to have PCs on the same VLAN, ping each other from different switches. 
```
Switch>enable
Switch#configure terminal
Switch(config)#interface fastethernet 0/1 <and so on, depending on what ports you'd like>
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan <number>
<if the vlan doesn't exist, it'll be created>
Switch(config-if)#exit
Switch(config)#interface gigabitethernet 0/1
Switch(config-if)#switchport mode access
Switch(config-if)#switchport mode trunk
Switch(config-if)#exit
Switch(config)#
```
Note that all PCs will have to be given IPs, along with the VLANs. Oh, and PCs can't have the same IP just because they're on a different switch (in hindsight, that's quite obvious).

Edit: [Cisco's Intro to VLANs](../../../02_Assets/01_Networking/VLAN/Cisco_Intro_to_VLANs.pdf)

Edit 2:

# DTP:
[Link to ITexammasters site - Cisco workbook ripoff](https://itexamanswers.net/3-5-5-packet-tracer-configure-dtp-instructions-answer.html)
- The commands for configuring DTP on an interface and changing the native VLAN:
    ```
    S0>enable
    S0#configure terminal
    S0(config)#interface [interface-type interface-number]
    S0(config-if)#switchport mode dynamic desirable
    S0(config-if)#switchport trunk native vlan <vlan_number>
    S0(config-if)#end
    S0#
    ```
**DO NOT CONFIGURE THE PORTS AS ACCESS PORTS. THE CONFIGURATION WILL BECOME NON-NEGIOTIABLE, AND THE ONLY SOLUTION I'VE FOUND IS TO REMOVE THE SWITCH AND START FROM SCRATCH.**

The command `show interfaces trunk` will show all interfaces configured in `trunk` mode (`dynamic desirable` sets the ports on trunk unless it notices a corresponding access port).

# There are 3 main types of Inter-VLAN:
1. ## Legacy method:
    - Notes:
        - We're using two physical interfaces of the router to facilitate communication between 2 VLANs.
        - The more VLANs you have, the more difficult it gets to accomodate them.
    - Sample Commands:
        - R0:
            ```
            R0>enable
            R0#configure terminal
            R0(config)#interface [interface-type interface-number]
            R0(config-if)#ip address <ip_address> <subnet_mask>
            ```
        - S0:
            ```
            S0>enable
            S0#configure terminal
            S0(config)#vlan <vlan_id>
            S0(config-vlan)#exit
            S0(config)#interface [interface-type interface-number]
            S0(config-if)#switchport mode trunk
            S0(config-if)#switchport trunk native vlan <vlan_id>
            S0(config-if)#interface [interface-type interface-number]
            S0(config-if)#switchport mode access
            S0(config-if)#switchport access vlan <vlan_id>
            ```
2. ## Router on a stick:
    - Notes:
        - Well, as we only have 1 connection between router and switch; we'll be trunking it from the switch side and create sub-interfaces from the router side.
        - No need to assign ports to specific VLANs on the switch anymore.
        - Have to create as many sub-interfaces as there are VLANs. Each sub-interface will be the default-gateway for that specific VLAN.
        - The *number* after `encapsulation dot1Q` is the VLAN id. Don't mess that up.
        - **DO NOT FORGET** to switch on the interface of the router. I kept wondering what had gone wrong; I'm stupid sometimes.
        - Trunk ports are usually handed over to a native VLAN separate from the others. I tend to use `vlan 99` as a native VLAN. It's a best-practice in the industry.
    - Sample Commands:
        - Example for sub-interface: GigabitEthernet0/0/1.10
        - R0:
            ```
            R0>enable
            R0#configure terminal
            R0(config)#interface [interface-type interface-number]
            R0(config-if)#no shutdown
            R0(config-if)#interface <sub-interface and sub-interface_id>
            R0(config-if)#encapsulation dot1Q 10
            R0(config-if)#ip address <ip_address> <subnet_mask>
            ```
        - S0:
            ```
            S0>enable
            S0#configure terminal
            S0(config)#vlan <vlan_id>
            S0(config-vlan)#exit
            S0(config)#interface [interface-type interface-number]
            S0(config-if)#switchport mode trunk
            S0(config-if)#switchport trunk native vlan <vlan_id>
            S0(config-if)#interface [interface-type interface-number]
            S0(config-if)#switchport mode access
            S0(config-if)#switchport access vlan <vlan_id>
            ```
3. ## Inter-VLAN routing on an L3 switch:
    - Notes:
        - SVIs (Switch Virtual Interfaces) act like router interfaces; they have an IP address, and a global default-gateway is defined in the switch.
        - The switch part is exactly the same. You make VLANs, assign them IPs (making them into SVIs). Conmfiguring access ports for the devices connected to different VLANs.
        - The only extra command is `ip routing` which we have to type in the configuration mode. This enables L3 routing (and consequently inter-VLAN routing) in the switch. **DO NOT FORGET THIS COMMAND.**
        - Unlike router-on-a-stick, this configuration will not require any trunk ports. This is assuming that you don't use an L3 switch as a central routing hub for other L2 switches, in which case you will need to use trunk ports. But if a port on this switch is directly connected to a host, then the port can be set to `access mode`. However, if you do need to configure trunk ports, the commands are given.
        - If you need to reach the VLANs from another network, you will need to configure routing on the ports. This is very simple, just type `no switchport` for whatever is your public WAN facing port.
        - Configuring routing protocols is the same as on a router.
    - Scenario 1: Inter-VLAN routing on the same LAN.
        - Sample commands:
            ```
            Switch(config)#vlan <vlan_id>
            Switch(config-vlan)#name <name>       // This step is optional.
            Switch(config-vlan)#exit
            Switch(config)#interface vlan <vlan_id>
            Switch(config-if)#ip address <ip_address> <subnet_mask>
            Switch(config-if)#no shutdown
            Switch(config-if)#exit
            Switch(config)#interface [interface-type interface-number]
            Switch(config-if)#description <some_description>  //Optional step.
            Switch(config-if)#switchport mode access
            Switch(config-if)#switchport access vlan <vlan_id>
            Switch(config-if)#exit
            Switch(config)#ip routing
            ```
    - Scenario 2: Inter-VLAN routing reachable from a remote network.
        - Sample commands:
            ```
            Switch(config)#interface vlan <vlan_id>
            Switch(config-if)#ip address <ip_address> <subnet_mask>
            Switch(config-if)#no shutdown
            Switch(config-if)#interface [interface-type interface-number]
            Switch(config-if)#switchport mode access
            Switch(config-if)#switchport access vlan <vlan_id>
            Switch(config-if)#exit
            Switch(config)#ip routing
            Switch(config)#interface [interface-type interface-number]
            Switch(config-if)#no switchport
            Switch(config-if)#ip address <ip_address> <subnet_mask>
            Switch(config-if)#no shutdown
            Switch(config-if)#exit
            Switch(config)#router rip
            Switch(config-if)#version 2
            Switch(config-if)#no auto-summary
            Switch(config-if)#network <network range>
            Switch(config-if)#passive-interface [interface]
            Switch(config-if)#default-information originate
            Switch(config-if)#end
            ```
            - Used RIP as an example. Cisco CCNA 2 syllabus has demonstrated OSPF.
        - Sample commands to configure **trunk ports**:
            ```
            Switch(config)#interface [interface-type interface-number]
            Switch(config-if)#switchport mode trunk
            Switch(config-if)#switchport trunk native vlan <vlan_id>
            Switch(config-if)#switchport trunk encapsulation dot1q
            ```
            - That's right. We also have to specify the encapsulation. I can rationalise it by saying that VLAN tagging is only supported by the 802.1Q standard, but the truth is I don't know. This is close to the same reason for encapsulation the virtual sub-interfaces in router-on-a-stick.

# VTP:

## VTP server:
```
S1(config)#vtp mode server
Device mode already VTP SERVER.
S1(config)#vtp domain CCNA
Changing VTP domain name from NULL to CCNA
S1(config)#vtp password cisco
Setting device VLAN database password to cisco
```

## VTP client:
```
S2(config)#vtp mode client
Setting device to VTP CLIENT mode.
S2(config)#vtp domain CCNA
Domain name already set to CCNA.
S2(config)#vtp password cisco
Setting device VLAN database password to cisco
```

## Staying transparent:
```
S3(config)#vtp mode Transparent 
Setting device to VTP TRANSPARENT mode.
S3(config)#vtp domain CCNA
Domain name already set to CCNA.
S3(config)#vtp password cisco
Setting device VLAN database password to cisco
```

## For reference and checking configuration:
```
S1#show vtp status
S1#show vtp counters
S1#show vtp password
```

The only difference between a transparent machine and a client machine, is that VLANs will propagate to the client machine, while the transparent machine will not participate in VLAN propagation.\
This is from [Cisco's website](https://www.cisco.com/c/en/us/support/docs/lan-switching/vtp/10558-21.html#:~:text=VTP%20server%20is%20the%20default,do%20not%20participate%20in%20VTP.)

# Troubleshooting:
- `do show interface [interface-type interface-number] switchport`
- `do show interface trunk`
- `show interfaces trunk`
- `do show vlan brief`
- `show run`
- `do show vlan brief`

# A few resources:
Ok, so the idea I had was wrong. By quite a margin. No, you don't open ports between VLANs for them to communicate between each other. Instead, you configure a trunk port between your router and your switch (called a trunk port), and the inter-VLAN packets go through it.

Essentially, when the PDU can't find the specified L2 address (MAC address) in the VLAN, it sends the packet to the router. The router searches for the L3 address (IP address), finds it in another VLAN (for that, enable both VLANs in the trunk port), and sends it to the L3 address of the host on the other VLAN. This is what I've understood, of course there's lots more to it.

Creating a trunk port between a router and a switch is called *Router on a stick*. This is done because the router might have limited ports available. Links below:
- [Router on a stick implementation in Packet Tracer.](https://www.geeksforgeeks.org/configuration-of-router-on-a-stick/)
- [Write-up on Access and Trunk ports.](https://www.geeksforgeeks.org/access-trunk-ports/)
- [Routing between VLANs using sub interfaces i.e. *Router on a stick*.](https://www.practicalnetworking.net/stand-alone/routing-between-vlans/#subinterfaces)
- [Network Engineering Stack exchange answer to why we need routers (or any other L3 device like an L3 switch) for inter VLAN communication.](https://networkengineering.stackexchange.com/questions/58454/why-do-vlans-need-routers-to-communicate)
- [Configuring the trunk port with Pfsense and managed switches from a variety of vendors. It seems that Netgear switches resemble those of TPLink.](https://docs.netgate.com/pfsense/en/latest/recipes/switch-vlan-configuration.html?highlight=trunk#configure-trunk-port)
- [Prerequisites to Trunking.](https://docs.netgate.com/pfsense/en/latest/vlan/index.html?highlight=trunk)
- [VLAN Trunking Protocol](https://en.wikipedia.org/wiki/VLAN_Trunking_Protocol)
- [Configuring VTP in packet tracer](https://www.cisco.com/c/en/us/support/docs/lan-switching/vtp/98154-conf-vlan.html)
