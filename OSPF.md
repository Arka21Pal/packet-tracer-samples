# OSPF in packet tracer

```
Router(config)#interface Loopback <loopback_id>
Router(config-if)#ip address <ip_address> <network_mask>
Router(config-if)#exit
Router(config)#router ospf 10
Router(config-router)#network <IP range> <wildcard mask> <area area_id>
Router(config-router)#passive-interface <interface_name_id>
Router(config-router)#router-id <desired router_id>
Router(config-router)#Reload or use "clear ip ospf process" command, for this to take effect
Router(config-router)#end
Router#clear ip ospf process
Reset ALL OSPF processes? [no]: yes
Router#show ip protocols
```

## Modifications:
- Configuring individual interface with specific process_id and area.
    ```
    Router(config)#interface [interface-type interface-number]
    Router(config-if)#ip ospf <process_id> area <area_id>
    ```
    - Doing this will yield an output for individual `show ip ospf interface` commands. As an example - 
        ```
        R2#show ip ospf interface gig 0/0/0

        GigabitEthernet0/0/0 is up, line protocol is up
        Internet address is 192.168.1.3/24, Area 0
        Process ID 10, Router ID 3.3.3.3, Network Type BROADCAST, Cost: 1
        Transmit Delay is 1 sec, State DR, Priority 1
        Designated Router (ID) 3.3.3.3, Interface address 192.168.1.3
        Backup Designated Router (ID) 1.1.1.1, Interface address 192.168.1.1
        Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
            Hello due in 00:00:06
        Index 1/1, flood queue length 0
        Next 0x0(0)/0x0(0)
        Last flood scan length is 1, maximum is 1
        Last flood scan time is 0 msec, maximum is 0 msec
        Neighbor Count is 2, Adjacent neighbor count is 2
            Adjacent with neighbor 1.1.1.1  (Backup Designated Router)
            Adjacent with neighbor 2.2.2.2
        Suppress hello for 0 neighbor(s)
        ```
- Configuring priorities for individual interfaces, *a router can be a DR or BDR at once in 2 different networks*.
    ```
    Router(config)#interface [interface-type interface-number]
    Router(config-if)#ip ospf priority <0-255>
    ```
    - putting `0` means that interface can never become DR or BDR.
    - putting `255` will make that interface the DR (Designated Router).
    - putting `1` will make that interface the BDR (Backup Designated Router).
- Configuring point-to-point for an individual interface (Loopback taken as an example).
    ```
    Router(config)#interface Loopback <loopback_id>
    Router(config-if)#ip ospf network point-to-point
    ```
    - Use the interface configuration command `ip ospf network point-to-point` on all interfaces where you want to disable the DR/BDR election process.
- Configuring passive-interface for an individual interface (Loopback taken as an example).
    ```
    Router(config)#interface Loopback <loopback_id>
    Router(config-if)#no switchport
    Router(config-if)#ip ospf passive-interface
    ```
- Configuring Hello and Dead intervals.
    ```
    Router(config-if)# ip ospf hello-interval seconds
    Router(config-if)# ip ospf dead-interval seconds
    ```
    - Dead Interval time is automatically set to 4x that of the Hello Time interval.
    - Both Hello Time and Dead Time intervals need to be consistent between 2 routers (at least). As it is configured per-interface, I'd assume it can be different for different areas. No guarantees of course.

# As an example:

```
Router#show ip protocols 

Routing Protocol is "ospf 10"
    Outgoing update filter list for all interfaces is not set 
    Incoming update filter list for all interfaces is not set 
    Router ID 1.1.1.1
    Number of areas in this router is 1. 1 normal 0 stub 0 nssa
    Maximum path: 4
    Routing for Networks:
        10.1.1.4 0.0.0.3 area 0
        10.1.1.12 0.0.0.3 area 0
    Passive Interface(s): 
        Loopback0
    Routing Information Sources:  
        Gateway         Distance      Last Update 
        1.1.1.1              110      00:07:14
        2.2.2.2              110      00:07:14
        3.3.3.3              110      00:07:14
    Distance: (default is 110)
```

# Reference:

- [OSPF Configuration Step by Step Guide](https://www.computernetworkingnotes.com/ccna-study-guide/ospf-configuration-step-by-step-guide.html)
- [How to configure Multi-Area OSPF in Packet Tracer](https://computernetworking747640215.wordpress.com/2019/11/06/how-to-configure-multi-area-ospf-in-packet-tracer/)