# Routing is important in tunnel implementations.

Steps for a router:
- Give IP addresses, configure devices.
- Create a static route so that the internet-facing port of source router can reach the public network of destination router.
- Steps for GRE tunnel:
    ```
    Router(config)#interface tun0
    Router(config-if)#ip address <ip address of tunnel> <subnet mask of tunnel>
    Router(config-if)#tunnel source <relevant ethernet port>
    Router(config-if)#tunnel destination <public IP of destination>
    Router(config-if)#tunnel mode gre ip
    Router(config-if)#end
    ```
- Create another static rule:
    - Network: private network range of destination router.
    - Subnet: Subnet of private range of destination router.
    - Next hop: Tunnel IP of destination router.

**Steps are mandatory for both routers across the ends of a tunnel.**