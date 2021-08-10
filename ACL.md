# Contents
- [Configure Standard IPv4 ACLs](#Configure-Standard-IPv4-ACLs)
    - [Numbered Standard IPv4 ACL Example](#Numbered-Standard-IPv4-ACL-Example)
    - [Named Standard IPv4 ACL Example](#Named-Standard-IPv4-ACL-Example)
    - [Access-lists for an interface (commands)](#Access-lists-for-an-interface-(commands))
        - [Add a remark](#Add-a-remark)
        - [Define rule for a single IP](#Define-rule-for-a-single-IP)
        - [Define rule for a network/range of IPs](#Define-rule-for-a-network/range-of-IPs)
        - [Apply a Standard IPv4 ACL](#Apply-a-Standard-IPv4-ACL)
        - [Numbered Standard IPv4 ACL](#Numbered-Standard-IPv4-ACL)
        - [Named Standard IPv4 ACL](Numbered-Standard-IPv4-ACL)
        - [Editing an access-list from its group](#Editing-an-access-list-from-its-group)
    - [Access-lists for remote administrative access (vty lines)](#Access-lists-for-remote-administrative-access-(vty-lines))
- [Troubleshoot](#Troubleshoot)

# Configure Standard IPv4 ACLs

- ## Numbered Standard IPv4 ACL Example

    ```
    R1(config)# access-list 10 remark ACE permits ONLY host 192.168.10.10 to the internet
    R1(config)# access-list 10 permit host 192.168.10.10
    R1(config)# access-list 10 remark ACE permits all host in LAN 2
    R1(config)# access-list 10 permit 192.168.20.0 0.0.0.255
    R1(config)# interface Serial 0/1/0
    R1(config-if)# ip access-group 10 out
    R1(config-if)# end
    R1# show run | section access-list

    access-list 10 remark ACE permits host 192.168.10.10
    access-list 10 permit 192.168.10.10
    access-list 10 remark ACE permits all host in LAN 2
    access-list 10 permit 192.168.20.0 0.0.0.255

    R1# show ip int Serial 0/1/0 | include access list

      Outgoing Common access list is not set
      Outgoing access list is 10
      Inbound Common access list is not set
      Inbound  access list is not set
    ```

- ## Named Standard IPv4 ACL Example

    ```
    R1(config)# no access-list 10
    R1(config)# ip access-list standard PERMIT-ACCESS
    R1(config-std-nacl)# remark ACE permits host 192.168.10.10
    R1(config-std-nacl)# permit host 192.168.10.10
    R1(config-std-nacl)# remark ACE permits all hosts in LAN 2
    R1(config-std-nacl)# permit 192.168.20.0 0.0.0.255
    R1(config-std-nacl)# exit
    R1(config)# interface Serial 0/1/0
    R1(config-if)# ip access-group PERMIT-ACCESS out
    R1(config-if)# end
    R1# show access-lists

    Standard IP access list PERMIT-ACCESS
        10 permit 192.168.10.10
        20 permit 192.168.20.0, wildcard bits 0.0.0.255

    R1# show run | section ip access-list

    ip access-list standard PERMIT-ACCESS
     remark ACE permits host 192.168.10.10
     permit 192.168.10.10
     remark ACE permits all hosts in LAN 2
     permit 192.168.20.0 0.0.0.255

    R1# show ip int Serial 0/1/0 | include access list

      Outgoing Common access list is not set
      Outgoing access list is PERMIT-ACCESS
      Inbound Common access list is not set
      Inbound  access list is not set
    ```

- ## Access-lists for an interface (commands)
    - ### Add a remark
        ```
        R1(config)# access-list access-list-number remark [remark upto 100 characters]
        ```
    - ### Define rule for a single IP
        ```
        R1(config)# access-list access-list-number permit host ip-address
        ```
    - ### Define rule for a network/range of IPs
        ```
        R1(config)# access-list access-list-number permit network wildcard-mask
        ```
    - ### Apply a Standard IPv4 ACL
        - After a standard IPv4 ACL is configured, it must be linked to an interface or feature. The following command can be used to bind a numbered or named standard IPv4 ACL to an interface:
            ```
            Router(config-if) # ip access-group {access-list-number | access-list-name} {in | out}
            ```
    - ### Numbered Standard IPv4 ACL
        ```
        Router(config)# access-list access-list-number {deny | permit | remark text} source [source-wildcard] [log]
        ```
        - Use the ` no access-list [access-list-number]` global configuration command to remove a numbered standard ACL.
    - ### Named Standard IPv4 ACL
        ```
        Router(config)# ip access-list standard access-list-name
        ```
    - ### Editing an access-list from its group
        ```
        R1# show access-lists
        R1# configure terminal
        R1(config)# ip access-list standard access-list-number
        R1(config-std-nacl)# no access-list-number
        R1(config-std-nacl)# access-list-number { permit|deny } host [ip-address]
        // This is just for an example, the whole range of commands are open here
        R1(config-std-nacl)# end
        R1# show access-lists
        ```

- ## Access-lists for remote administrative access (vty lines)
    - ### The `access-class` Command
        ```
        R1(config-line)# access-class {access-list-number | access-list-name} { in | out } 
        ```
    - ### Secure VTY Access Example
        - A named standard ACL called ADMIN-HOST is created and identifies PC1. Notice that the deny any has been configured to track the number of times access has been denied. The vty lines are configured to use the local database for authentication, permit Telnet traffic, and use the ADMIN-HOST ACL to restrict traffic.
            ```
            R1(config)# username ADMIN secret class
            R1(config)# ip access-list standard ADMIN-HOST
            R1(config-std-nacl)# remark This ACL secures incoming vty lines
            R1(config-std-nacl)# permit 192.168.10.10
            R1(config-std-nacl)# deny any
            R1(config-std-nacl)# exit
            R1(config)# line vty 0 4
            R1(config-line)# login local
            R1(config-line)# transport input telnet
            R1(config-line)# access-class ADMIN-HOST in
            R1(config-line)# end
            R1#
            ```
        - In a production environment, you would set the vty lines to only allow SSH, as shown in the example.
            ```
            R1(config)# line vty 0 4
            R1(config-line)# login local
            R1(config-line)# transport input ssh
            R1(config-line)# access-class ADMIN-HOST in
            R1(config-line)# end
            R1#
            ```

# Configure Extended IPv4 ACLs
- ## Individual commands
    - ### Numbered Extended IPv4 ACL Syntax
        ```
        Router(config)# access-list access-list-number {deny | permit | remark text} protocol source source-wildcard [operator {port}] destination destination-wildcard [operator {port}] [established] [log]
        ```
        - `operator`: 
            - (Optional) This compares source or destination ports.
            - Some operators include lt (less than), gt (greater than), eq (equal), and neq (not equal).
        - `port`:
            - (Optional) The decimal number or name of a TCP or UDP port.
        - `established`:
            - (Optional) For the TCP protocol only.
            - This is a 1st generation firewall feature.
        - `log`:
            - (Optional) This keyword generates and sends an informational message whenever the ACE is matched.
            - This message includes ACL number, matched condition (i.e., permitted or denied), source address, and number of packets.
            - This message is generated for the first matched packet.
            - This keyword should only be implemented for troubleshooting or security reasons.
        - The command to apply an extended IPv4 ACL to an interface is the same as the command used for standard IPv4 ACLs.
            ```
            Router(config-if)# ip access-group {access-list-number | access-list-name} {in | out}
            ```
        - #### Protocols and Ports
            - `<0-255>` - An IP protocol number
            - `ahp` - Authentication Header Protocol
            - `dvmrp` - dvmrp
            - `eigrp` - Cisco's EIGRP routing protocol
            - `esp` - Encapsulation Security Payload
            - `gre` - Cisco's GRE tunneling
            - `icmp` - Internet Control Message Protocol
            - `igmp` - Internet Gateway Message Protocol
            - `ip` - Any Internet Protocol
            - `ipinip` - IP in IP tunneling
            - `nos` - KA9Q NOS compatible IP over IP tunneling
            - `object-group` - Service object group
            - `ospf` - OSPF routing protocol
            - `pcp` - Payload Compression Protocol
            - `pim` - Protocol Independent Multicast
            - `tcp` - Transmission Control Protocol
            - `udp` - User Datagram Protocol
    - ### Named Extended IPv4 ACL Syntax
        ```
        Router(config)# ip access-list extended access-list-name 
        ```
- ## Protocols and Port Numbers Configuration Examples
    - This example configures an extended ACL 100 to filter HTTP traffic. The first ACE uses the www port name. The second ACE uses the port number 80. Both ACEs achieve exactly the same result.
        ```
        R1(config)# access-list 100 permit tcp any any eq www
        R1(config)#  !or...
        R1(config)# access-list 100 permit tcp any any eq 80 
        ```
    - Configuring the port number is required when there is not a specific protocol name listed such as SSH (port number 22) or an HTTPS (port number 443).
        ```
        R1(config)# access-list 100 permit tcp any any eq 22
        R1(config)# access-list 100 permit tcp any any eq 443
        ```
- ## Numbered Extended IPv4 ACL Example
    ```
    R1(config)# access-list 110 permit tcp 192.168.10.0 0.0.0.255 any eq www
    R1(config)# access-list 110 permit tcp 192.168.10.0 0.0.0.255 any eq 443
    R1(config)# interface g0/0/0
    R1(config-if)# ip access-group 110 in
    R1(config-if)# exit
    R1(config)#
    ```
- ## TCP Established Extended ACL Example
    ```
    R1(config)# access-list 120 permit tcp any 192.168.10.0 0.0.0.255 established
    R1(config)# interface g0/0/0 
    R1(config-if)# ip access-group 120 out 
    R1(config-if)# end
    R1# show access-lists 
    Extended IP access list 110
        10 permit tcp 192.168.10.0 0.0.0.255 any eq www
        20 permit tcp 192.168.10.0 0.0.0.255 any eq 443 (657 matches)
    Extended IP access list 120
        10 permit tcp any 192.168.10.0 0.0.0.255 established (1166 matches)
    R1#
    ```
- ## Named Extended IPv4 ACL Example
    ```
    R1(config)# ip access-list extended SURFING
    R1(config-ext-nacl)# Remark Permits inside HTTP and HTTPS traffic 
    R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 80
    R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 443
    R1(config-ext-nacl)# exit
    R1(config)# ip access-list extended BROWSING
    R1(config-ext-nacl)# Remark Only permit returning HTTP and HTTPS traffic 
    R1(config-ext-nacl)# permit tcp any 192.168.10.0 0.0.0.255 established
    R1(config-ext-nacl)# exit
    R1(config)# interface g0/0/0
    R1(config-if)# ip access-group SURFING in
    R1(config-if)# ip access-group BROWSING out
    R1(config-if)# end
    R1# show access-lists
    Extended IP access list SURFING
        10 permit tcp 192.168.10.0 0.0.0.255 any eq www
        20 permit tcp 192.168.10.0 0.0.0.255 any eq 443 (124 matches) 
    Extended IP access list BROWSING
        10 permit tcp any 192.168.10.0 0.0.0.255 established (369 matches) 
    R1#
    ```
    - SURFING - This will permit inside HTTP and HTTPS traffic to exit to the internet.
    - BROWSING - This will only permit returning web traffic to the inside hosts while all other traffic exiting the R1 G0/0/0 interface is implicitly denied.
    - For clarification of commands check the Cisco docs or try [this](#Numbered-Extended-IPv4-ACL-Syntax).
- ## Souped up Named Extended IPv4 ACL Example
    - Services permitted: FTP, SSH, Telnet, DNS, HTTP, and HTTPS traffic.
    - PERMIT-PC1 - This will only permit PC1 TCP access to the internet and deny all other hosts in the private network.
        - The PERMIT-PC1 ACL permits PC1 (i.e., 192.168.10.10) TCP access to the FTP (i.e., ports 20 and 21), SSH (22), Telnet (23), DNS (53), HTTP (80), and HTTPS (443) traffic. DNS (53) is permitted for both TCP and UDP.
    - REPLY-PC1 - This will only permit specified returning TCP traffic to PC1 implicitly deny all other traffic.
        - The REPLY-PC1 ACL will permit return traffic to PC1.
    - The PERMIT-PC1 ACL is applied inbound and the REPLY-PC1 ACL applied outbound on the R1 G0/0/0 interface.
    ```
    R1(config)# ip access-list extended PERMIT-PC1
    R1(config-ext-nacl)# Remark Permit PC1 TCP access to internet 
    R1(config-ext-nacl)# permit tcp host 192.168.10.10 any eq 20
    R1(config-ext-nacl)# permit tcp host 192.168.10.10 any eq 21
    R1(config-ext-nacl)# permit tcp host 192.168.10.10 any eq 22
    R1(config-ext-nacl)# permit tcp host 192.168.10.10 any eq 23
    R1(config-ext-nacl)# permit udp host 192.168.10.10 any eq 53
    R1(config-ext-nacl)# permit tcp host 192.168.10.10 any eq 53
    R1(config-ext-nacl)# permit tcp host 192.168.10.10 any eq 80
    R1(config-ext-nacl)# permit tcp host 192.168.10.10 any eq 443
    R1(config-ext-nacl)# deny ip 192.168.10.0 0.0.0.255 any 
    R1(config-ext-nacl)# exit
    R1(config)# 
    R1(config)# ip access-list extended REPLY-PC1
    R1(config-ext-nacl)# Remark Only permit returning traffic to PC1 
    R1(config-ext-nacl)# permit tcp any host 192.168.10.10 established
    R1(config-ext-nacl)# exit
    R1(config)# interface g0/0/0
    R1(config-if)# ip access-group PERMIT-PC1 in
    R1(config-if)# ip access-group REPLY-PC1 out
    R1(config-if)# end
    R1#
    ```
- ## Editing Extended ACLs
    - ### Example for editing Extended ACL
        ```
        R1# configure terminal
        R1(config)# ip access-list extended SURFING 
        R1(config-ext-nacl)# no 10
        R1(config-ext-nacl)# 10 permit tcp 192.168.10.0 0.0.0.255 any eq www
        R1(config-ext-nacl)# end
        ```
    - Like standard ACLs, an extended ACL can be edited using a text editor when many changes are required. Otherwise, if the edit applies to one or two ACEs, then sequence numbers can be used.
    - To correct an error using sequence numbers, the original statement is removed with the `no access-list-number` command and the corrected statement is added replacing the original statement.

# Troubleshoot
```
R1# show run | section access-list
```
```
R1# show ip int [interface interface-id] | include access list
```