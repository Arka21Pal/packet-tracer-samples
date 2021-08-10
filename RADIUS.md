# Sample configuration for RADIUS authentication on Cisco IOS router.

```
R1(config)# radius-server host [host ip] auth-port [auth-port: default is 1645] key [secret-key defined in RADIUS]
R1(config)# aaa authentication login default group radius local
R1(config)# line vty <0-15>
R1(config-line)# login authentication default 
R1(config-line)# end
```

- Exit out of User EXEC mode. You will be prompted to enter your username and password. Once entered, it will take a few seconds to check your credentials and then will log you in.
- Do not forget to set a hostname. **Use that same hostname as name of host in the RADIUS server**.
- If you change the `auth-port` on the router, remember to change it on the RADIUS server too. And vice-versa.