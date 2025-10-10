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

<br>
 
2. PC "192.168.1.4" seems to be unable to ping other PCs in the network. Check switch configuration.

  TIP : How many broadcast domains are there in this network ?

<br>

3. Choose the right cable to connect :

    - Switch0 gigabitethernet 1/1 to Switch1 gigabitethernet 1/1
    - Switch1 gigabitethernet 1/2 to Switch2 gigabitethernet 1/2

<br>

4. Configure those two links as trunk lines without using trunk negotiation between switches

<br>

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

**Commands on Switch0** :

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

Check your config:

```sh
show interfaces status
```

You should see all ports set to 100Mb/s and Full Duplex.

<br>

### STEP 2 — Fix Connectivity for 192.168.1.4 (Laptop)

If the laptop cannot ping others:

1. Ensure it’s connected to **VLAN 1** on Switch0
2. Check that the interface is **up** and not administratively down.

Check with:

```sh
show vlan brief
```

If FastEthernet0/4 is not under VLAN 1, fix it:

```sh
interface FastEthernet0/4
 switchport mode access
 switchport access vlan 1
 no shutdown
```

Then test again:
From Laptop (192.168.1.4) → ping 192.168.1.1

<br>

### STEP 3 — Correct Cabling Between Switches

| Connection                    | Cable Type          | Why                              |
| ----------------------------- | ------------------- | -------------------------------- |
| Switch0 Gi0/1 → Switch1 Gi1/1 | **Crossover cable** | Same device type (Switch↔Switch) |
| Switch1 Gi0/2 → Switch2 Gi1/2 | **Crossover cable** | Same device type (Switch↔Switch) |


> Tip: In newer Cisco hardware, Auto-MDIX can auto-detect cable type, but this lab disables auto-negotiation, so use crossover manually.

<br>

### STEP 4 — Configure Trunk Links (No Negotiation)

You will manually set the trunk ports (disable DTP negotiation).

On Switch0:

```sh
interface GigabitEthernet0/1
 switchport mode trunk
 no shutdown
 exit
```

<br>

On Switch1:

```sh
interface GigabitEthernet0/1
 switchport mode trunk
 no shutdown
 exit
interface GigabitEthernet0/2
 switchport mode trunk
 no shutdown
 exit
```

<br>

On Switch2:

```sh
interface GigabitEthernet1/2
 switchport mode trunk
 no shutdown
 exit
```

<br>

### STEP 5 — Verify Trunk Links

Check if your trunk links are operational:

```sh
show interfaces trunk
```

Expected Output:

```sh
Port        Mode         Encapsulation  Status        Native vlan
Gi0/1       on           802.1q         trunking      1
Gi0/2       on           802.1q         trunking      1
```

Both `Administrative Mode` and `Operational Mode` should be `trunk`.

<br>

**Optional: Useful Show Commands**

| Command                      | Purpose                         |
| ---------------------------- | ------------------------------- |
| `show running-config`        | Review all port settings        |
| `show vlan brief`            | Verify VLANs and access ports   |
| `show interfaces switchport` | View port modes and negotiation |
| `show interfaces trunk`      | Verify trunking details         |

<br>

### STEP 6 — Test Connectivity

From any PC/Laptop, test connectivity:

```sh
ping 192.168.1.1
ping 192.168.1.2
ping 192.168.1.3
ping 192.168.1.4
```

If all pings succeed → You completed the lab perfectly.

---