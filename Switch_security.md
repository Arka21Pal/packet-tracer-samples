# Implement Port Security:

- Enable Port Security:
    ```
    S1>enable
    S1#configure terminal
    S1(config)#interface [interface-type interface-number]
    S1(config-if)#switchport mode access
    S1(config-if)#switchport port-security
    S1(config-if)#end
    S1#show port-security [interface-type interface-number]
    ```
- Limit and Learn MAC Addresses:
    ```
    S1(config)# interface f0/1
    S1(config-if)# switchport port-security maximum <number>
    ```
    - The switch can be configured to learn about MAC addresses on a secure port in one of three ways:
        - Manually Configured:
            ```
            Switch(config-if)# switchport port-security mac-address mac-address
            ```
            - Tip: When it gives some error like `duplicate mac-address found`, try this:
                ```
                SW-1(config-if)#no switchport port-security 
                SW-1(config-if)#no switchport port-security maximum 4
                SW-1(config-if)#no switchport port-security mac-address <mac-address>
                ```
                - Just remove all port-security, and do it again. It works!!!
        - Dynamically Learned:
            - When the `switchport port-security` command is entered, the current source MAC for the device connected to the port is automatically secured but is not added to the startup configuration. If the switch is rebooted, the port will have to re-learn the device’s MAC address.
        - Dynamically Learned – Sticky:
            ```
            Switch(config-if)# switchport port-security mac-address sticky 
            ```
            - The administrator can enable the switch to dynamically learn the MAC address and *stick* them to the running configuration.
- Port Security Aging:
    ```
    Switch(config-if)# switchport port-security aging { static | time time | type {absolute | inactivity}}
    ```
    - `static`: Enable aging for statically configured secure addresses on this port.
    - `time` *`time`*: Specify the aging time for this port. The range is 0 to 1440 minutes. If the time is 0, aging is disabled for this port.
    - `type absolute`: Set the absolute aging time. All the secure addresses on this port age out exactly after the time (in minutes) specified and are removed from the secure address list.
    - `type inactivity`: Set the inactivity aging type. The secure addresses on this port age out only if there is no data traffic from the secure source address for the specified time period.
    - Example: 
        ```
        S1(config)# interface fa0/1
        S1(config-if)# switchport port-security aging time 10 
        S1(config-if)# switchport port-security aging type inactivity 
        S1(config-if)# end
        ```
- Port Security Violation Modes:
    ```
    Switch(config-if)# switchport port-security violation { protect | restrict | shutdown}
    ```
    - `shutdown` (default): The port transitions to the error-disabled state immediately, turns off the port LED, and sends a syslog message. It increments the violation counter. When a secure port is in the error-disabled state, an administrator must re-enable it by entering the `shutdown` and `no shutdown` commands.
    - `restrict`: 	The port drops packets with unknown source addresses until you remove a sufficient number of secure MAC addresses to drop below the maximum value or increase the maximum value. This mode causes the Security Violation counter to increment and generates a syslog message.
    - `protect`: This is the least secure of the security violation modes. The port drops packets with unknown MAC source addresses until you remove a sufficient number of secure MAC addresses to drop below the maximum value or increase the maximum value. No syslog message is sent.
    - Troubleshoot:
        ```
        S1# show interface fa0/1 | include down
        ```
- Verify Port Security:
    ```
    S1# show port-security
    ```
    ```
    S1# show port-security interface fastethernet 0/1
    ```
    ```
    S1# show run interface fa0/1
    ```
    ```
    S1# show port-security address
    ```

# Mitigate VLAN hopping attacks:

- Disable DTP (auto trunking) negotiations on non-trunking ports by using the `switchport mode access interface` configuration command.
- Disable unused ports and put them in an unused VLAN.
- Manually enable the trunk link on a trunking port by using the `switchport mode trunk` command.
- Disable DTP (auto trunking) negotiations on trunking ports by using the `switchport nonegotiate` command.
- Set the native VLAN to a VLAN other than VLAN 1 by using the `switchport trunk native vlan vlan_number` command.

# Mitigate DHCP Attacks:
- DHCP Snooping:
    - Enable DHCP snooping by using the `ip dhcp snooping` global configuration command.
        ```
        S1(config)# ip dhcp snooping
        ```
    - On trusted ports, use the `ip dhcp snooping trust` interface configuration command.
        ```
        S1(config)# interface f0/1
        S1(config-if)# ip dhcp snooping trust
        S1(config-if)# exit
        ```
    - Limit the number of DHCP discovery messages that can be received per second on untrusted ports by using the `ip dhcp snooping limit rate` interface configuration command.
        ```
        S1(config)# interface range f0/5 - 24
        S1(config-if-range)# ip dhcp snooping limit rate 6
        S1(config-if-range)# exit
        ```
    - Enable DHCP snooping by VLAN, or by a range of VLANs, by using the `ip dhcp snooping vlan` global configuration command.
        ```
        S1(config)# ip dhcp snooping vlan 5,10,50-52
        S1(config)# end
        S1#
        ```
    - Verify.
        ```
        S1# show ip dhcp snooping
        ```

# Mitigate ARP Attacks:
- Enable DHCP snooping globally.
    ```
    S1(config)# ip dhcp snooping
    ```
- Enable DHCP snooping on selected VLANs.
    ```
    S1(config)# ip dhcp snooping vlan 10
    ```
- Enable DAI on selected VLANs.
    ```
    S1(config)# ip arp inspection vlan 10
    ```
- Configure trusted interfaces for DHCP snooping and ARP inspection.
    ```
    S1(config)# interface fa0/24
    S1(config-if)# ip dhcp snooping trust
    S1(config-if)# ip arp inspection trust
    ```
- Configure to check for source-mac, destination-mac or ip address.
    ```
    S1(config)# ip arp inspection validate {[src-mac] [dst-mac] [ip]}
    ```
    - Verify.
        ```
        S1(config)# do show run | include {[src-mac] [dst-mac] [ip]}
        ```

# Mitigate STP Attacks:
- Configure PortFast.
    ```
    S1(config)# interface fa0/1
    S1(config-if)# switchport mode access
    S1(config-if)# spanning-tree portfast
    S1(config-if)# exit
    S1(config)# spanning-tree portfast default
    S1(config)# exit
    ```
    - Verify.
        ```
        S1# show running-config | begin span
        ```
    - PortFast bypasses the STP listening and learning states to minimize the time that access ports must wait for STP to converge.
        - If PortFast is enabled on a port connecting to another switch, there is a risk of creating a spanning-tree loop.
- Configure BPDU Guard.
    ```
    S1(config)# interface fa0/1
    S1(config-if)# spanning-tree bpduguard enable
    S1(config-if)# exit
    S1(config)# spanning-tree portfast bpduguard default
    S1(config)# end
    ```
    - Even though PortFast is enabled, the interface will still listen for BPDUs. Unexpected BPDUs might be accidental, or part of an unauthorized attempt to add a switch to the network.
    - If any BPDUs are received on a BPDU Guard enabled port, that port is put into error-disabled state.
        - This means the port is shut down and must be manually re-enabled or automatically recovered through the `errdisable recovery cause bpduguard` global command.

- Verify.
    ```
    S1# show spanning-tree summary
    ```