# Tips:
- `enable secret` and `enable password` achieve the same thing (set a password to enter privileged exec mode), however the former utilises encryption by default whilst the latter doesn't.

# Commands for hardening a device running Cisco IOS.

- ## Changing the hostname:
    ```
    Router(config)# hostname Router
    ```
- ## Set encrypted privileged exec password (prevent unauthorised access):
    ```
    Router(config)# enable secret [unencrypted-password]
            or
    Router(config)# enable secret [encryption-type encrypted-password]
    ```
- ## Secure Access Lines into the device:
    ```
    Router(config)# line console 0
    Router(config-line)# password [unencrypted-password]
    Router(config-line)# login
            or
    Router(config-line)# login local
    ```
    ```
    Router(config)# line vty [0-15]
    Router(config-line)# password [unencrypted-password]
    Router(config-line)# login
            or
    Router(config-line)# login local
    ```
    - Use `login local` only when you've configured a [local user and password](#Configure-local-user-and-password:). 
    - ### Configure a protocol more secure than Telnet for in-band management connections (SSH):
        - Configure a domain name:
            ```
            Router(config)# ip domain-name [domain-name]
            ```
        - Generate RSA key-pair:
            ```
            Router(config)# crypto key generate rsa
            ```
            - Generally we use `1024` or `2048` here.
        - Enable SSHv2:
            ```
            Router(config)# ip ssh version 2
            Router(config)# ip ssh time-out [5-120]
            ```
        - Configure the lines to use SSH over Telnet:
            ```
            Router(config)# line vty [0-15]
            Router(config-line)# transport input ssh
            ```
        - Now, to access the device over an ssh connection, we need to define a [username and a password locally](#Configure-local-user-and-password:) on the device (assuming it's not already configured).
- ## Configure minimum length for passwords:
    ```
    Router(config)# security passwords min-length [0-16]
    ```
- ## Encrypt plaintext passwords in the running-configuration:
    ```
    Router(config)# service password-encryption 
    ```
    - This encryption method is quite weak and can be easily reversed, when compared to the one-way md5 hash used by the method `secret`.
- ## Configure local user and password:
    ```
    Router(config)# username [username] password [password]
    ```
