# Managing the clock

NTP servers are arranged in levels, with each level called a `stratum`. `Stratum 0` contains authoritative time sources like Atomic Clocks. `Stratum 1` contains network devices directly connected to `Stratum 0` which then relay time to the lower levels. `Stratum 2` onwards, are devices which connect to the `Stratum 1` servers through network connections.

- The `show clock` command displays the current time on the software clock.
    ```
    R1# show clock detail
    ```
- The `ntp server ip-address` command is issued in global configuration mode to configure an NTP server for the device.
    ```
    R1(config)# ntp server 209.165.200.225 
    R1(config)# end 
    R1# show clock detail 
    21:01:34.563 UTC Fri Nov 15 2019
    Time source is NTP
    ```
- The `show ntp associations` and `show ntp status` commands are used to verify that the device is synchronized with the NTP server.
    ```
    R1# show ntp associations
    ```
    ```
    R1# show ntp status 
    ```