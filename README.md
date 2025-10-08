# Packet Tracer 8.2.2 - Lab 2 : Interfaces configuration

## Nwtwork diagram

This lab will test your ability to configure **speed**, **duplex**, and **vlan settings** on the network interfaces of a Catalyst 2960 switch using Cisco Packet Tracer 8.1.1 .

![Nwtwork diagram](images/network-diagram.png)

<br>

## Lab instructions

1. Connect to Switch0 using console interface and configure each Switch0 fastethernet switchport for operation. Correct settings are :
  - Port type : access port
  - Speed : 100 Mbit/s
  - Duplex mode : Full Duplex
  - Autonegotiation disabled
 
2. PC "192.168.1.4" seems to be unable to ping other PCs in the network. Check switch configuration.

TIP : How many broadcast domains are there in this network ?


3. Choose the right cable to connect :
  - Switch0 gigabitethernet 1/1 to Switch1 gigabitethernet 1/1
  - Switch1 gigabitethernet 1/2 to Switch2 gigabitethernet 1/2


4. Configure those two links as trunk lines without using trunk negotiation between switches


Copyright : http://www.packettracernetwork.com

<br>

---

## Solution

### STEP 1 — Configure Switch0 Access Ports

Goal: Set each FastEthernet port on Switch0 to:

- Mode: access
- Speed: 100
- Duplex: full
- Disable auto-negotiation (by setting speed manually)

<br>

**Commands on Switch0 **:

```sh
Switch> enable
Switch# configure terminal

! Port for PC-PT (192.168.1.1)
interface FastEthernet0/1
 switchport mode access
 duplex full
 speed 100
 exit

! Port for PC-PT (192.168.1.2)
interface FastEthernet0/2
 switchport mode access
 duplex full
 speed 100
 exit

! Port for PC-PT (192.168.1.3)
interface FastEthernet0/3
 switchport mode access
 duplex full
 speed 100
 exit

! Port for Laptop-PT (192.168.1.4)
interface FastEthernet0/4
 switchport mode access
 duplex full
 speed 100
 switchport access vlan 1
 exit
```