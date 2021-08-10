## Duplex and Mdix mode:

```
Switch>enable
Switch#configure terminal
Switch(config)#hostname S1
S1(config)#interface fastethernet 0/1
S1(config-if)#duplex auto
S1(config-if)#speed auto
S1(config-if)#mdix auto
S1(config-if)#end
S1#copy running-config startup-config
<press enter>
```

![PIC of duplex and mdix configuration](../../02_Assets/01_Networking/packet_tracer/duplex_mdix/duplex.jpg)

Alternatively, if you didn't want mdix and wanted a constant speed rating, replace the commands above with these:

```
S1(config-if)#speed 100 <for example>
```