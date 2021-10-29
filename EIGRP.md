# `EIGRP` (Packet Tracer)

**Only ever use the networks to which the router is directly connected**.

```
router(config)# router eigrp <autonomous-system-number>
router(config-router)# network <network>
router(config-router)# network <network>
router(config-router)# no auto-summary
router(config-router)# end
```

## Specific example
```
router(config)# router eigrp 100
router(config-router)# network 10.0.0.0
router(config-router)# network 20.0.0.0
router(config-router)# no auto-summary
router(config-router)# end
```

### Tid-bits:
- signified by protocol 88 in the IP header.
- `TLV` field contains `EIGRP` parameters, IP internal and external routes.
- EIGRP for IPv6 is encapsulated with a multicast address (FF02::A) and same code (88)


## Resources
- [EIGRP Configuration With Packet Tracer](https://ipcisco.com/lesson/eigrp-configuration-with-packet-tracer-ccnp/)
- [Enhanced Interior Gateway Routing Protocol](https://www.cisco.com/c/en/us/support/docs/ip/enhanced-interior-gateway-routing-protocol-eigrp/16406-eigrp-toc.html)
- [EIGRP no auto-summary and wildcard](https://learningnetwork.cisco.com/s/question/0D53i00000Kswim/eigrp-no-autosummary-and-wildcard)
- [EIGRP Message Authentication Configuration Example](https://www.cisco.com/c/en/us/support/docs/ip/enhanced-interior-gateway-routing-protocol-eigrp/82110-eigrp-authentication.html)
