# CISCO2

###############Zadani se trmi switchi##########



1. Adresace- 121.121.0.0/16
######################################################################################################
VLAN	ID	Adresa		Mask.	S1 SVI		S2 SVI		HRSP		KLIENT
------------------------------------------------------------------------------------------------------
vlan	11	121.121.11.0	24	121.121.11.2	121.121.11.3	121.121.11.1	121.121.11.10
vlan	22	121.121.22.0	24	121.121.22.2	121.121.22.3	121.121.22.1	121.121.22.10
vlan	33	-		-	-		-		-		-
Link S1-S2	121.121.100.0	30(252)	121.121.100.1	121.121.100.2	-		-
------------------------------------------------------------------------------------------------------
PC	vlan 11		121.121.11.10/24	gw 121.121.11.1
------------------------------------------------------------------------------------------------------
---------------------------------
	S1
----------------------------------
hostname S1
no ip domain-lookup
spanning-tree mode pvst
ip routing

VLAN a SVI
-----------
vlan 11
 name First
vlan 22
 name Second
vlan 33
 name Native

------------------------------------------------------
!--– LACP EtherChannel k S3 –--
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

!--– Routed link k S2 –--
interface GigabitEthernet1/0/3
  no switchport
  ip address 121.121.100.1 255.255.255.252
  no shutdown
--------------------------------------------------------
interface Vlan11
  ip address 121.121.11.2 255.255.255.0
  standby 11 ip 121.121.11.1
  standby 11 priority 110
  standby 11 preempt
  no shutdown

interface Vlan22
  ip address 121.121.22.2 255.255.255.0
  standby 22 ip 121.121.22.1
  standby 22 priority 110
  standby 22 preempt
  no shutdown
--------------------------------------------------------
	S2
--------------------------------------------------------
hostname S2
no ip domain-lookup
spanning-tree mode pvst
ip routing

--------------------------------------------------------
!--– VLANy a SVIs –--
vlan 11
 name First
vlan 22
 name Second
vlan 33
 name Native

-----------------------------------------------------------
!--– PAgP EtherChannel k S3 –--
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
----------------------------------------------------------
interface Vlan11
  ip address 121.121.11.3 255.255.255.0
  standby 11 ip 121.121.11.1
  standby 11 preempt
  no shutdown

interface Vlan22
  ip address 121.121.22.3 255.255.255.0
  standby 22 ip 121.121.22.1
  standby 22 preempt
  no shutdown
-----------------------------------------------------------
!--– Routed link k S1 –--
interface GigabitEthernet1/0/3
  no switchport
  ip address 121.121.100.2 255.255.255.252
  no shutdown
----------------------------------------------------------
	S3
------------------------------------------------------
hostname S3
no ip domain-lookup
spanning-tree mode pvst
--------------------------------------------
!--– VLANy –--
vlan 11
 name First
vlan 22
 name Second
vlan 33
 name Native
-----------------------------------------------------
!--– LACP EtherChannel k S1 –--
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
-------------------------------------------------------
!--– PAgP EtherChannel k S2 –--
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
-----------------------------------------------------------
!--– Access port pro PC1 (VLAN11) –--
interface FastEthernet0/24
  switchport mode access
  switchport access vlan 11
  spanning-tree portfast
  no shutdown
