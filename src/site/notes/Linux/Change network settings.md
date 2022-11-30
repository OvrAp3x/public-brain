---
dg-publish: true
tags:
- public
- linux
- networking
---

## Task: Display current IP address and setting for network interface called eth0

Use ifconfig command:

`# ifconfig eth0`

Output:
```
eth0      Link encap:Ethernet  HWaddr 00:30:48:5A:BF:46
         inet addr:10.5.123.2  Bcast:10.5.123.63  Mask:255.255.255.192
         inet6 addr: fe80::230:48ff:fe5a:bf46/64 Scope:Link
         UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:728204 errors:0 dropped:0 overruns:0 frame:0
         TX packets:1097451 errors:0 dropped:0 overruns:0 carrier:0
         collisions:0 txqueuelen:1000
         RX bytes:62774749 (59.8 MiB)  TX bytes:1584343634 (1.4 GiB)
         Interrupt:177
```
## Task: Change IP address

You can change ip address using ifconfig command itself (temporarily, until next reboot). To set IP address 192.168.1.5, enter command:
```
ifconfig eth0 192.168.1.5 netmask 255.255.255.0 up
ifconfig eth0
```
to change permanently edit
`nano /etc/network/interfaces`

edit the file for the desired settings, example below

Eksempel på statisk ip config
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inte loopback

#The primary network interface
auto eth0
iface eth0 inet static
address 192.168.0.101
netmask 255.255.255.0
network 192.168.0.0
broadcast 192.168.0.255
gateway 192.168.0.1
```
Restarte Interfaces

/etc/init.d/networking restart
Eksempel på DHCP ip config:

```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inte loopback

#The primary network interface
auto eth0
iface eth0 inet dhcp
```
Restarte interfaces

/etc/init.d/networking restart

