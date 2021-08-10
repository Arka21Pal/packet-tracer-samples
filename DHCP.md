# DHCPv4.

## Sample Configuration to make a router into a DHCPv4 server.

- ### Router as central DHCP server.
    ```
    Router>enable
    Router#configure terminal
    Router(config)#hostname R1
    R1(config)#interface g0/0/1
    R1(config-if)#ip address 192.168.10.1 255.255.255.0
    R1(config-if)#no shutdown
    R1(config)#ip dhcp excluded-address 192.168.10.1 192.168.10.10
    R1(config)#ip dhcp pool LAN-POOL-1
    R1(dhcp-config)#network 192.168.10.0 255.255.255.0
    R1(dhcp-config)#default-router 192.168.10.1
    R1(dhcp-config)#dns-server 192.168.11.5
    R1(dhcp-config)#domain-name example.com
    R1(dhcp-config)#end
    ```
    - `ip dhcp excluded-address` excludes a range of addresses.
    - `default-router` is the default gateway.
- ### Router relaying from central DHCP server.
    - Router connected to switch, central DHCP server located elsewhere on the network (Relay). This can also be used by intermediate routers to point to central server/router serving out DHCP addresses, by pointing to their IP address ona particular interface.
    ```
    R1(config)# interface [interface-type interface-number]
    R1(config-if)# ip helper-address 192.168.11.6
    R1(config-if)# end
    R1#
    ```
    - The interface for which the DHCP relay is being configured *should be connected to the hosts*, either directly or via a switch.
    - > A better solution is to configure R1 with the `ip helper-address` address interface configuration command. This will cause R1 to relay DHCPv4 broadcasts to the DHCPv4 server.
        - `192.168.11.6` is the IP of the central DHCP server in the example.
    - > DHCPv4 is not the only service that the router can be configured to relay. By default, the ip helper-address command forwards the following eight UDP services: 
        - Port 37: Time
        - Port 49: TACACS
        - Port 53: DNS
        - Port 67: DHCP/BOOTP server
        - Port 68: DHCP/BOOTP client
        - Port 69: TFTP
        - Port 137: NetBIOS name service
        - Port 138: NetBIOS datagram service

Assuming that we set up a DNS server with that domain name, this should work.

**Tip: Do it in parts. Configure the `pool`, `default-router` and `network` first. Then add the DNS server.**

## To make a router into a DHCPv4 client (if you have a dynamic address from an ISP).

```
R2(config)#interface [interface-type interface-number]
R2(config-if)#ip address dhcp
R2(config-if)#no shut
```

## Troubleshoot:
- `show running-config | section dhcp`
- `show ip dhcp binding`
- `show ip dhcp server statistics`
- On the PC: `ipconfig /all`
    - `ipconfig /renew` to resend the DHCPDISCOVER message.
- To stop the DHCP service: `R1(config)# no service dhcp`.

# DHCPv6

## Sample Configuration to make a router into a DHCPv6 server:

- ### Configuring a Stateless DHCPv6 Server.
    - Enable IPv6 routing.
        ```
        Router(config)# ipv6 unicast-routing
        ```
    - Define a DHCPv6 pool name.
        ```
        Router(config)# ipv6 dhcp pool IPV6-STATELESS
        // Put any name - this is from the Cisco example.
        ```
    - Configure the DHCPv6 pool.
        ```
        Router(config-dhcpv6)# dns-server 2001:db8:acad:1::254
        Router(config-dhcpv6)# domain-name example.com
        Router(config-dhcpv6)# exit
        ```
    - Bind the DHCPv6 pool to an interface.
        ```
        Router(config)# interface GigabitEthernet0/0/1
        Router(config-if)# description Link to LAN
        Router(config-if)# ipv6 address fe80::1 link-local
        Router(config-if)# ipv6 address 2001:db8:acad:1::1/64
        Router(config-if)# ipv6 nd other-config-flag
        Router(config-if)# ipv6 dhcp server IPV6-STATELESS
        Router(config-if)# no shut
        Router(config-if)# end
        ```
    - All addresses and interface names/numbers used here are only used as examples.
- ### Configuring a Stateful DHCPv6 Server.
    - Enable IPv6 routing.
        ```
        Router(config)# ipv6 unicast-routing
        ```
    - Define a DHCPv6 pool name.
        ```
        Router(config)# ipv6 dhcp pool IPV6-STATEFUL
        ```
    - Configure the DHCPv6 pool.
        ```
        Router(config-dhcpv6)# address prefix 2001:db8:acad:1::/64
        Router(config-dhcpv6)# dns-server 2001:4860:4860::8888
        Router(config-dhcpv6)# domain-name example.com
        Router(config-dhcpv6)#
        ```
    - Bind the DHCPv6 pool to an interface.
        ```
        Router(config)# interface GigabitEthernet0/0/1
        Router(config-if)# description Link to LAN
        Router(config-if)# ipv6 address fe80::1 link-local
        Router(config-if)# ipv6 address 2001:db8:acad:1::1/64
        Router(config-if)# ipv6 nd managed-config-flag
        Router(config-if)# ipv6 nd prefix default no-autoconfig
        Router(config-if)# ipv6 dhcp server IPV6-STATEFUL
        Router(config-if)# no shut
        Router(config-if)# end
        Router#
        ```
        - The DHCPv6 pool has to be bound to the interface using the ipv6 dhcp server *POOL-NAME* interface config command.
            - The M flag is manually changed from 0 to 1 using the interface command `ipv6 nd managed-config-flag`.
                - You can use the `no ipv6 nd managed-config-flag` command to set the M flag back to its default of 0.
            - The A flag is manually changed from 1 to 0 using the interface command `ipv6 nd prefix default no-autoconfig`.
                - The `no ipv6 nd prefix default no-autoconfig` command sets the A flag back to its default of 1.
                - >The A flag can be left at 1, but some client operating systems such as Windows will create a GUA using SLAAC and get a GUA from the stateful DHCPv6 server. Setting the A flag to 0 tells the client not to use SLAAC to create a GUA.
            - >The `ipv6 dhcp server` command binds the DHCPv6 pool to the interface. The Router will now respond with the information contained in the pool when it receives stateful DHCPv6 requests on this interface.
    - All addresses and interface names/numbers used here are only used as examples.
- ### Configuring a DHCPv6 Relay Agent.
    ```
    Router(config-if)# ipv6 dhcp relay destination <ipv6-address> [interface-type interface-number]
    ```
    - For example:
        ```
        R1(config)# interface gigabitethernet 0/0/1
        R1(config-if)# ipv6 dhcp relay destination 2001:db8:acad:1::2 G0/0/0
        R1(config-if)# exit
        R1(config)#
        ```
    - Verify the relay agent:
        ```
        Router# show ipv6 dhcp interface
        ```
        ```
        Client# show ipv6 dhcp binding
        ```


## Sample Configuration to make a router into a DHCPv6 client:

- ### Configuring a Stateless DHCPv6 Client.
    - Enable IPv6 routing.
        ```
        Router(config)# ipv6 unicast-routing
        ```
    - Configure client router to create an LLA.
        ```
        Router(config)# interface g0/0/1
        Router(config-if)# ipv6 enable
        ```
    - Configure client router to use SLAAC.
        ```
        Router(config-if)# ipv6 address autoconfig
        Router(config-if)# end
        Router#
        ```
    - Verify client router is assigned a GUA.
        ```
        Router# show ipv6 interface brief
        ```
    - Verify client router received other DHCPv6 information.
        ```
        Router# show ipv6 dhcp interface g0/0/1
        ```
    - All addresses and interface names/numbers used here are only used as examples.

- ### Configuring a Stateful DHCPv6 client.
    - Enable IPv6 routing.
        ```
        R3(config)# ipv6 unicast-routing
        ```
    - Configure client router to create an LLA.
        ```
        R3(config)# interface g0/0/1
        R3(config-if)# ipv6 enable
        R3(config-if)# 
        ```
    - Configure client router to use DHCPv6.
        ```
        R3(config-if)# ipv6 address dhcp
        R3(config-if)# end
        R3#
        ```
    - Verify client router is assigned a GUA.
        ```
        R3# show ipv6 interface brief
        ```
    - Verify client router received other DHCPv6 information.
        ```
        R3# show ipv6 dhcp interface g0/0/1
        ```

## Enabling different modes:
- SLAAC:
    ```
    R1(config)# ipv6 unicast-routing
    ```
- Stateless DHCPv6:
    ```
    R1(config)# interface g0/0/1
    R1(config-if)# ipv6 nd other-config-flag
    ```
- Stateful DHCPv6:
    ```
    R1(config)# int g0/0/1
    R1(config-if)# ipv6 nd managed-config-flag
    R1(config-if)# ipv6 nd prefix default no-autoconfig
    R1(config-if)# end
    ```

## Troubleshooting:
- `show ipv6 dhcp pool`
- `show ipv6 dhcp binding`
Use these commands in privileged EXEC mode.