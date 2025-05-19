###Cisco

zadani se tremi sw
```vhdl
<!--
Adresní rozsah: 121.121.0.0/16

+---------+-----+----------------+------------+---------------+---------------+---------------+----------------+
| VLAN    | ID  | Adresa         | Maska      | S1 SVI        | S2 SVI        | HSRP          | Klient         |
+---------+-----+----------------+------------+---------------+---------------+---------------+----------------+
| VLAN 11 | 11  | 121.121.11.0   | 255.255.255.0 | 121.121.11.2 | 121.121.11.3 | 121.121.11.1 | 121.121.11.10 |
| VLAN 22 | 22  | 121.121.22.0   | 255.255.255.0 | 121.121.22.2 | 121.121.22.3 | 121.121.22.1 | 121.121.22.10 |
| VLAN 33 | 33  | nativní VLAN   | -            | -             | -             | -             | -              |
| Link S1-S2     | -   | 121.121.100.0  | 255.255.255.252 | 121.121.100.1 | 121.121.100.2 | -             | -              |
+---------+-----+----------------+------------+---------------+---------------+---------------+----------------+

PC1 (VLAN 11):
IP: 121.121.11.10/24  
Gateway: 121.121.11.1

hostname S1
no ip domain-lookup
spanning-tree mode pvst
ip routing

vlan 11
 name First
vlan 22
 name Second
vlan 33
 name Native

# LACP EtherChannel k S3
interface range GigabitEthernet1/0/1-2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 33
 switchport trunk allowed vlan 11,22,33
 channel-protocol lacp
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 description LACP S1<>S3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 33
 switchport trunk allowed vlan 11,22,33
 channel-protocol lacp
 no shutdown

# Routed link k S2
interface GigabitEthernet1/0/3
 no switchport
 ip address 121.121.100.1 255.255.255.252
 no shutdown

# SVI VLAN11 + HSRP
interface Vlan11
 ip address 121.121.11.2 255.255.255.0
 standby 11 ip 121.121.11.1
 standby 11 priority 110
 standby 11 preempt
 no shutdown

# SVI VLAN22 + HSRP
interface Vlan22
 ip address 121.121.22.2 255.255.255.0
 standby 22 ip 121.121.22.1
 standby 22 priority 110
 standby 22 preempt
 no shutdown

hostname S2
no ip domain-lookup
spanning-tree mode pvst
ip routing

vlan 11
 name First
vlan 22
 name Second
vlan 33
 name Native

# PAgP EtherChannel k S3
interface range GigabitEthernet1/0/1-2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 33
 switchport trunk allowed vlan 11,22,33
 channel-group 2 mode desirable
 no shutdown

interface Port-channel2
 description PAgP S2<>S3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 33
 switchport trunk allowed vlan 11,22,33
 no shutdown

# SVI VLAN11 + HSRP
interface Vlan11
 ip address 121.121.11.3 255.255.255.0
 standby 11 ip 121.121.11.1
 standby 11 preempt
 no shutdown

# SVI VLAN22 + HSRP
interface Vlan22
 ip address 121.121.22.3 255.255.255.0
 standby 22 ip 121.121.22.1
 standby 22 preempt
 no shutdown

# Routed link k S1
interface GigabitEthernet1/0/3
 no switchport
 ip address 121.121.100.2 255.255.255.252
 no shutdown

hostname S3
no ip domain-lookup
spanning-tree mode pvst

vlan 11
 name First
vlan 22
 name Second
vlan 33
 name Native

# LACP EtherChannel k S1
interface range FastEthernet0/1-2
 no channel-group
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 33
 switchport trunk allowed vlan 11,22,33
 channel-protocol lacp
 channel-group 1 mode passive
 no shutdown

interface Port-channel1
 description LACP S3<>S1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 33
 switchport trunk allowed vlan 11,22,33
 channel-protocol lacp
 no shutdown

# PAgP EtherChannel k S2
interface range FastEthernet0/3-4
 no channel-group
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 33
 switchport trunk allowed vlan 11,22,33
 channel-group 2 mode auto
 no shutdown

interface Port-channel2
 description PAgP S3<>S2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 33
 switchport trunk allowed vlan 11,22,33
 no shutdown

# Access port pro PC1 (VLAN 11)
interface FastEthernet0/24
 switchport mode access
 switchport access vlan 11
 spanning-tree portfast
 no shutdown
-->
