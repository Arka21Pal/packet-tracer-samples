# Steps for NAT and PAT.
There are many types of protocols used for address translation. Here, we're looking at NAT (dynamic) and PAT (and static NAT).

- ## Sample configuration for Dynamic NAT.
    - NAT pool:
        ```
        Router(config)#interface [interface-type interface-number]
        Router(config-if)#ip nat <inside/outside>
        // Note, the WAN connection is assigned 'outside' and the LAN is assigned 'inside'
        Router(config-if)#exit
        Router(config)#access-list <access_list_number> permit <network_range> <wildcard mask>
        Router(config)#ip nat pool <pool_name> <starting_ip_address> <ending_ip_address> netmask <mask for the network>
        Router(config)#ip nat inside source list <access_list_number> pool <pool_name>
        ```
        - `starting_ip_address` and `ending_ip_address` are the starting and ending addresses for the nat pool.
        - Be absolutely sure to use the same nat pool name and the same ACL number.
    - It might not work the first time (2021-04-05: I tried the same commands 10 times but it didn't work. And later that day, it magically started working. Utter rubbish)
- ## Sample configuration for PAT (Port address Translation).
    - Same steps as Dynamic NAT before, but instead of using an ACL (in this case anyway), just use static mappings. Takes care of static NAT too.
        ```
        Router(config)#ip nat inside source static <tcp/udp> <internal_ip_address> <port> <external_ip_address> <port> 
        ```
    - Interface Overloading:
        ```
        Router(config)#interface [interface-type interface-number]
        Router(config-if)#ip nat <inside/outside>
        // Note, the WAN connection is assigned 'outside' and the LAN is assigned 'inside'
        Router(config-if)#exit
        Router(config)#access-list <access_list_number> permit <network_range> <wildcard mask>
        Router(config)#ip nat inside source list <access_list_number> interface [interface-type interface-number] overload
        ```
        - The obvious advantage is that I don't have to declare a NAT pool. In fact, this is now my favourite method, over the more purist one above.
    - Address pool:
        ```
        R2(config)# ip nat pool NAT-POOL2 209.165.200.226 209.165.200.240 netmask 255.255.255.224
        R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
        R2(config)# ip nat inside source list 1 pool NAT-POOL2 overload
        R2(config)# 
        R2(config)# interface serial0/1/0
        R2(config-if)# ip nat inside
        R2(config-if)# exit
        R2(config)# interface serial0/1/1
        R2(config-if)# ip nat outside
        R2(config-if)# end
        R2#
        ```
    - This is essentially port-forwarding in Cisco IOS.
    - The protocols `tcp` and `udp` vary by use-case.
    - Just like you do it in any other router OS, just specify the internal IP address and port, then open a port on the WAN side, while mapping it.
- ## Clearing the translation table
    - Clear all dynamic address translation entries from the NAT translation table.
        ```
        clear ip nat translation *
        ```
    - Clear a simple dynamic translation entry containing an inside translation or both inside and outside translation.
        ```
        clear ip nat translation inside global-ip local-ip [outside local-ip global-ip]
        ```
    - Clear an extended dynamic translation entry.
        ```
        clear ip nat translation protocol inside global-ip global-port local-ip local-port [ outside local-ip local-port global-ip global-port]
        ```
    - **Note**: Only the dynamic translations are cleared from the table. Static translations cannot be cleared from the translation table.

# Guide

- NAT64
    - [How to Configure Stateful Network Address Translation 64](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_nat/configuration/xe-16/nat-xe-16-book/iadnat-stateful-nat64.html#GUID-6F2A56B4-A210-43C6-9DCA-321068B24FED)
    - [Static NAT-PT for IPv6 Configuration Example](https://www.cisco.com/c/en/us/support/docs/ip/network-address-translation-nat/113275-nat-ptv6.html)
    - [Configuring Basic IPv6 to IPv4 Connectivity for NAT-PT for IPv6](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_nat/configuration/15-mt/nat-15-mt-book/ip6-natpt.html#GUID-B2222AC6-1CBF-4DFC-BBF4-2C1FF21CAEE6)
