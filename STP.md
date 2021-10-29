# The problem here, is that there is no TTL in the Layer 2.

Thus, there is nothing preventing a continuous broadcast of packets, especially things like `ARP`.\
That's why you need `STP`, because it will take care of the redundant links and also reduce the broadcast domain.

# Commands



# Resources

- https://www.ciscopress.com/articles/article.asp?p=2832407&seqNum=6
- https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst6500/ios/12-2SX/configuration/guide/book/stp_enha.html
- https://www.cisco.com/c/en/us/support/docs/smb/switches/cisco-small-business-300-series-managed-switches/smb5760-configure-stp-settings-on-a-switch-through-the-cli.html
- https://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r4-0/addr_serv/command/reference/ir40asrbook_chapter2.html
- https://community.cisco.com/t5/switching/show-arp-vs-show-mac-address-table/td-p/1584636
