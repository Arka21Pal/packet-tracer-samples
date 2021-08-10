# L2 protocols for Device Discovery 

- ## CDP
    - Enable CDP globally for device
        ```
        Router(config)# cdp run
        ```
        - Disable CDP globally for device   
            ```
            Router(config)# no cdp run
            ```
    - Enable CDP for specific interface
        ```
        Switch(config)# interface gigabitethernet 0/0/1
        Switch(config-if)# cdp enable
        ```
        - Disable CDP for specific interface
            ```
            Switch(config)# interface gigabitethernet 0/0/1
            Switch(config-if)# cdp enable
            ```
    - To verify the status of CDP and display information about CDP
        ```
        Router# show cdp
        ```
        - To display a list of neighbours
            ```
            Router# show cdp neighbors
            ```
            ```
            Router# show cdp neighbors detail
            ```
        - To display the interfaces that are CDP-enabled on a device
            ```
            Router# show cdp interface
            ```

- ## LLDP
    - Enable LLDP globally for device
        ```
        Switch(config)# lldp run
        ```
        - Disable LLDP globally for device
            ```
            Switch(config)# no lldp run
            ```
    - Enable LLDP for an interface
        ```
        Switch(config)# interface gigabitethernet 0/1
        Switch(config-if)# lldp transmit
        Switch(config-if)# lldp receive
        ```
        - Disable LLDP for an interface
            ```
            Switch(config)# interface gigabitethernet 0/1
            Switch(config-if)# no lldp transmit
            Switch(config-if)# no lldp receive
            ```
    - Verify neighbours using LLDP
        ```
        S1# show lldp neighbors
        ```
        ```
        S1# show lldp neighbors detail
        ```