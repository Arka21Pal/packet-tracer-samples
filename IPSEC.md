# Commands

```
Router(config)#crypto isakmp enable
Router(config)#crypto isakmp policy <number> 
//taking 10 here.

Router(config-isakmp)#hash md5
Router(config-isakmp)#authentication pre-share
Router(config-isakmp)#lifetime 86400
Router(config-isakmp)#group <number> 
//taking 5

Router(config-isakmp)#exit

Router(config)#crypto isakmp key 13876694751 address <Public IP of destination>
Router(config)#access-list <number> permit ip <source LAN range> <wildcard subnet> <destination LAN range> <wildcard subnet>
// taking access-list 110

Router(config)#crypto ipsec transform-set <transform-set name> esp-3des esp-md5-hmac
// taking the transform-set as 'test'

Router(config)#crypto map <map name> 10 ipsec-isakmp
// take map name as 'gre_map'

Router(config-crypto-map)#set peer <Public IP of destination>
Router(config-crypto-map)#set transform-set <transform-set name>
// taking the transform-set as 'test'

Router(config-crypto-map)#match address <access-list number>
Router(config-crypto-map)#exit

Router(config)#interface <public interface of source>
Router(config-if)#crypto map <map name>
Router(config-if)#exit

Router(config)#interface tun 0
Router(config-if)#ip address <IP of tun 0> <suitable subnet>
// taking the 192.168.1.0/24 range 

Router(config-if)#tunnel source <source of tunnel, public interface of source>
Router(config-if)#tunnel destination <Public IP of destination>
Router(config-if)#tunnel mode gre ip
Router(config-if)#exit

Router(config)#ip route <destination_private_network> <subnet_destination_private_network> <Tunnel_IP_destination_network>
Router(config)#access-list <number> permit gre host <Public IP of source> host <Public IP of destination>
```

# Note

- This works because the `access-list` is `110` i.e., it is an extended access-list. this gives many more abilities than normal access-lists.