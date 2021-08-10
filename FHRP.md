# First Hop Redundancy Protocols (FHRPs)

Used to bind together multiple routers (endpoints actually) for redundancy. This way, if a router goes down, you can still communicate with the outside internet. This is done by configuring a virtual default-gateway, binding that to routers, and keeping multiple routers standby for a hot-swap of service when the forwarding-router goes down.

# Sample Commands to configure HSRP (Hot Standby Router Protocol - Cisco-proprietary FHRP):

```
R1(config)#interface g0/1
R1(config-if)#standby version 2
R1(config-if)#standby 1 ip 192.168.1.254
R1(config-if)#standby 1 priority 150
R1(config-if)#standby 1 preempt 
R1(config-if)#end
```

- The interface being configured should be facing the LAN.
- The default-gateway should be changed to the virtual ip address in all hosts (and in DHCP servers).
- The `1` in `standby 1` signifies a group. A router can be part of multiple groups by varying this number.
- The command `standby 1 ip` is configuring the virtual default-gateway. Put this in every router being configured.
- The command `standby 1 priority` should be adjusted to the lowest value in a scale for the forwarding-router (main router). All others can be configured according to which router you'd like to swap in.
- The command `standby 1 preempt` configures a force re-check of priorities in all routers configured in the group. This is useful if the actual forwarding-router has just come back online and is needed to resume service.

# Troubleshooting:

- `show standby`
- `show standby brief`