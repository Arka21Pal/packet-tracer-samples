# IPv4

1. RIP
    In IPv4, we simply declare the networks we want to route dynamically between.
    ```
    Router>enable
    Router#configure terminal
    Router(config)#router rip
    Router(config-router)#version 2
    Router(config-router)#no auto-summary
    Router(config-router)#network <network range>
    Router(config-router)#network <network range>
    Router(config-router)#passive-interface [interface]
    Router(config-router)#default-information originate
    Router(config-router)#end
    ```
    - Passive interfaces:
        ```
        Router(config)#router rip
        Router(config-router)#passive-interface G0/0/2
        Router(config-router)#end
        ```
2. Static
    Static routing is very similar between IPv4 and IPv6
    ```
    Router>enable
    Router#configure terminal
    Router(config)#ip route <source network range> <subnet of source network> <interface used in source> <next hop> <administrative distance>
    // interface and administrative distance are optional parameters.
    ```
    - Default static route:
        ```
        Router(config)# ip route 0.0.0.0 0.0.0.0 {ip-address | exit-intf}
        ```
3. Check
    ```
    Router#show ip route
    ```

# IPv6

1. RIP
    - Per interface:

        ```
        Router>enable
        Router#configure terminal
        Router(config)#ipv6 unicast-routing
        Router(config)#interface <type> <number>
        Router(config-if)#ipv6 address <ipv6 address>
        Router(config-if)#ipv6 rip process1 enable
        Router(config-if)#ipv6 rip process1 default-information originate
        or 
        Router(config-if)#ipv6 rip RIP1 default-information originate
        ```
        It depends, writing `process1` is the Cisco way, we're just calling it `RIP1` here.

    - Customising RIP:

        ```
        Router>enable
        Router#configure terminal
        Router(config)#ipv6 router rip process1
        Router(config-router)#maximum-paths 1
        Router(config-if)#exit
        Router(config)#interface <type> <number>
        Router(config-if)#ipv6 rip process1 default-information originate
        ```

    - Verifying RIP
        ```
        Router#show ipv6 rip process1 database
        Router#show ipv6 route rip
        Router#show ipv6 route
        ```
2. Static Routing:
    ```
    Router>enable
    Router#configure terminal
    Router(config)#ipv6 unicast-routing
    Router(config)#interface <type> <number>
    Router(config-if)#ipv6 enable
    Router(config-if)#ipv6 address <ipv6 address>
    Router(config-if)#exit
    Router(config)#ipv6 route <destination network (keep the subnet like /64)> <next-hop>
    Router(config)#exit
    Router#show ipv6 route
    ```
    - Default static routing:
        ```
        Router(config)# ipv6 route ::/0 {ipv6-address | exit-intf}
        ```
3. Checking the interfaces:
    ```
    Router#show ipv6 interface brief
    ```

# Troubleshooting

```
Router#show cdp neighbors
Router#show ip protocols
Router#show ip route | begin Gateway
```

### Static routing vs RIP:
1. There's a notable difference: In RIP, the routers figure it out themselves. 
    - The output of `show ip route` generally shows a bunch of routes defined by using RIP (in my case).
    - The point is, you have to define exactly the same routes if you're going for static routing. Which is extremely annoying, to say the least.
    - Fox example, here's a sample output of `show ip route` while using RIP (only dynamic routes shown):
        ```
        R0:

        R    30.0.0.0/8 [120/1] via 10.0.0.1, 00:00:09, Serial0/1/1
        R    40.0.0.0/8 [120/1] via 20.0.0.1, 00:00:20, Serial0/1/0
        R    50.0.0.0/8 [120/2] via 10.0.0.1, 00:00:09, Serial0/1/1
        R    60.0.0.0/8 [120/2] via 20.0.0.1, 00:00:20, Serial0/1/0
        R    70.0.0.0/8 [120/3] via 10.0.0.1, 00:00:09, Serial0/1/1
                        [120/3] via 20.0.0.1, 00:00:20, Serial0/1/0
        R    198.0.0.0/24 [120/4] via 20.0.0.1, 00:00:20, Serial0/1/0
                        [120/4] via 10.0.0.1, 00:00:09, Serial0/1/1

        R1: 

        R    10.0.0.0/8 [120/1] via 20.0.0.2, 00:00:01, Serial0/1/0
            20.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
        R    30.0.0.0/8 [120/2] via 20.0.0.2, 00:00:01, Serial0/1/0
            40.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
        R    50.0.0.0/8 [120/2] via 40.0.0.2, 00:00:13, GigabitEthernet0/0/0
        R    60.0.0.0/8 [120/1] via 40.0.0.2, 00:00:13, GigabitEthernet0/0/0
        R    70.0.0.0/8 [120/2] via 40.0.0.2, 00:00:13, GigabitEthernet0/0/0
        R    192.168.10.0/24 [120/1] via 20.0.0.2, 00:00:01, Serial0/1/0
        R    192.168.20.0/24 [120/1] via 20.0.0.2, 00:00:01, Serial0/1/0
        R    198.0.0.0/24 [120/3] via 40.0.0.2, 00:00:13, GigabitEthernet0/0/0

        R2: 


        R    10.0.0.0/8 [120/2] via 40.0.0.1, 00:00:06, GigabitEthernet0/0/0
        R    20.0.0.0/8 [120/1] via 40.0.0.1, 00:00:06, GigabitEthernet0/0/0
        R    30.0.0.0/8 [120/2] via 60.0.0.2, 00:00:16, Serial0/1/0
            40.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
        R    50.0.0.0/8 [120/1] via 60.0.0.2, 00:00:16, Serial0/1/0
            60.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
        R    70.0.0.0/8 [120/1] via 60.0.0.2, 00:00:16, Serial0/1/0
        R    192.168.10.0/24 [120/2] via 40.0.0.1, 00:00:06, GigabitEthernet0/0/0
        R    192.168.20.0/24 [120/2] via 40.0.0.1, 00:00:06, GigabitEthernet0/0/0
        R    198.0.0.0/24 [120/2] via 60.0.0.2, 00:00:16, Serial0/1/0

        R3:

        R    20.0.0.0/8 [120/1] via 10.0.0.2, 00:00:03, Serial0/1/1
            30.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
        R    40.0.0.0/8 [120/2] via 10.0.0.2, 00:00:03, Serial0/1/1
        R    50.0.0.0/8 [120/1] via 30.0.0.2, 00:00:16, GigabitEthernet0/0/0
        R    60.0.0.0/8 [120/2] via 30.0.0.2, 00:00:16, GigabitEthernet0/0/0
        R    70.0.0.0/8 [120/2] via 30.0.0.2, 00:00:16, GigabitEthernet0/0/0
        R    192.168.10.0/24 [120/1] via 10.0.0.2, 00:00:03, Serial0/1/1
        R    192.168.20.0/24 [120/1] via 10.0.0.2, 00:00:03, Serial0/1/1
        R    198.0.0.0/24 [120/3] via 30.0.0.2, 00:00:16, GigabitEthernet0/0/0

        R4:

        R    10.0.0.0/8 [120/1] via 30.0.0.1, 00:00:04, GigabitEthernet0/0/0
        R    20.0.0.0/8 [120/2] via 30.0.0.1, 00:00:04, GigabitEthernet0/0/0
            30.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
        R    40.0.0.0/8 [120/2] via 50.0.0.2, 00:00:15, Serial0/1/1
            50.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
        R    60.0.0.0/8 [120/1] via 50.0.0.2, 00:00:15, Serial0/1/1
        R    70.0.0.0/8 [120/1] via 50.0.0.2, 00:00:15, Serial0/1/1
        R    192.168.10.0/24 [120/2] via 30.0.0.1, 00:00:04, GigabitEthernet0/0/0
        R    192.168.20.0/24 [120/2] via 30.0.0.1, 00:00:04, GigabitEthernet0/0/0
        R    198.0.0.0/24 [120/2] via 50.0.0.2, 00:00:15, Serial0/1/1

        R5:

        R    10.0.0.0/8 [120/2] via 50.0.0.1, 00:00:02, Serial0/1/1
        R    20.0.0.0/8 [120/2] via 60.0.0.1, 00:00:27, Serial0/1/0
        R    30.0.0.0/8 [120/1] via 50.0.0.1, 00:00:02, Serial0/1/1
        R    40.0.0.0/8 [120/1] via 60.0.0.1, 00:00:27, Serial0/1/0
            50.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
        R    192.168.10.0/24 [120/3] via 60.0.0.1, 00:00:27, Serial0/1/0
                            [120/3] via 50.0.0.1, 00:00:02, Serial0/1/1
        R    192.168.20.0/24 [120/3] via 60.0.0.1, 00:00:27, Serial0/1/0
                            [120/3] via 50.0.0.1, 00:00:02, Serial0/1/1
        R    198.0.0.0/24 [120/1] via 70.0.0.2, 00:00:15, GigabitEthernet0/0/0

        R6:

        R    10.0.0.0/8 [120/3] via 70.0.0.1, 00:00:18, GigabitEthernet0/0/0
        R    20.0.0.0/8 [120/3] via 70.0.0.1, 00:00:18, GigabitEthernet0/0/0
        R    30.0.0.0/8 [120/2] via 70.0.0.1, 00:00:18, GigabitEthernet0/0/0
        R    40.0.0.0/8 [120/2] via 70.0.0.1, 00:00:18, GigabitEthernet0/0/0
        R    50.0.0.0/8 [120/1] via 70.0.0.1, 00:00:18, GigabitEthernet0/0/0
        R    60.0.0.0/8 [120/1] via 70.0.0.1, 00:00:18, GigabitEthernet0/0/0
            70.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
        R    192.168.10.0/24 [120/4] via 70.0.0.1, 00:00:18, GigabitEthernet0/0/0
        R    192.168.20.0/24 [120/4] via 70.0.0.1, 00:00:18, GigabitEthernet0/0/0
            198.0.0.0/24 is variably subnetted, 2 subnets, 2 masks


        ```