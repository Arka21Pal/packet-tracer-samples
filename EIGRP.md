# `EIGRP` (Packet Tracer)

**Only ever use the networks to which the router is directly connected**.

```
router(config)# router eigrp <autonomous-system-number>
router(config-router)# network <network> <subnet-mask or wildcard bits>
router(config-router)# network <network> <subnet-mask or wildcard bits>
router(config-router)# passive-interface <interface-type> <interface-number>
router(config-router)# no auto-summary
router(config-router)# end
```

## Specific example
```
router(config)# router eigrp 100
router(config-router)# network 10.0.0.0 255.0.0.0
router(config-router)# network 20.0.0.0 255.0.0.0
router(config-router)# passive-interface G0/0/1
router(config-router)# no auto-summary
router(config-router)# end
```

## Edit: `IPv6` configuration
- Display the routing table for `IPv6`.
- Configure a directly connected `IPv6` default route.
- Configure the EIGRP routing process to propagate the default route.
```
router# show ipv6 route
router(config)# ipv6 route ::/0 serial 0/1/0
router(config)# ipv6 router eigrp 1
router(config-rtr)# redistribute static
```

### Tid-bits:
- The queue count is a measure of the congestion in an EIGRP network (the number of packets waiting for transmission)
- signified by protocol 88 in the IP header.
- `TLV` field contains `EIGRP` parameters, IP internal and external routes.
- EIGRP for IPv6 is encapsulated with a multicast address (FF02::A) and same code (88)
- To change the bandwidth of an interface: `bandwidth <kilobits-bandwidth-value>`. This can change EIGRP metrics.
- Configure `router id`:
    - `router id <ipv4-address>`
    - Highest loopback address
    - Highest active IP address of router

## Resources
- [EIGRP Configuration With Packet Tracer](https://ipcisco.com/lesson/eigrp-configuration-with-packet-tracer-ccnp/)
- [Enhanced Interior Gateway Routing Protocol](https://www.cisco.com/c/en/us/support/docs/ip/enhanced-interior-gateway-routing-protocol-eigrp/16406-eigrp-toc.html)
- [EIGRP no auto-summary and wildcard](https://learningnetwork.cisco.com/s/question/0D53i00000Kswim/eigrp-no-autosummary-and-wildcard)
- [EIGRP Message Authentication Configuration Example](https://www.cisco.com/c/en/us/support/docs/ip/enhanced-interior-gateway-routing-protocol-eigrp/82110-eigrp-authentication.html)
