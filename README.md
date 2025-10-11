# Packet Tracer 8.2.2 - Lab 4 Port security

## Introduction

A growing challenge for network administrators is to be able to control who is allowed - and who isn't - to access the organization's internal network. This access control is mandatory for critical infrastructure protection in your network. It is not on public parts of the network where guest users should be able to connect.

Port security is a feature implemented in Cisco Catalyst switches which helps network engineers in implementing network security on network boundaries.

In its most basic form, the Port Security feature remembers the MAC address of the device connected to the switch edge port and allows only that MAC address to be active on that port. If any other MAC address is detected on that port, port security feature shutdown the switch port.

The switch can be configured to send a SNMP trap to a network monitoring solution to alert that a port is disabled for security reasons.

<br>

## Network diagram

![network diagram](images/network-diagram.png)

<br>

## Lab instructions
This lab will test your ability to configure port security on CiscoTM 2960 switch interfaces.

1. Configure port security on interface Fa 0/1 of the switch with the following settings :

    - Port security enabled
    - Mode : restrict
    - Allowed mac addresses : 3
    - Dynamic mac address learning.

2. Configure port security on interface Fa 0/2 of the switch with the following settings :

    - Port security enabled
    - Mode : shutdown
    - Allowed mac addresses : 3
    - Dynamic mac address learning.

3. Configure port security on interface Fa 0/3 of the switch with the following settings :

    - Port security enabled
    - Mode : protect
    - Static mac address entry : 00E0.A3CE.3236

4. From LAPTOP 1 :

    Try to ping 192.168.1.2 and 192.168.1.3. It should work.
    Try to ping 192.168.1.4 and 192.168.1.5. It should work.

5. Connect ROGUE laptop to the hub.

    Try to ping 192.168.1.1. It should work.
    Try to ping 192.168.1.4. It should fail.

<br>

## Solution

### Lab Objective

You will configure **port security** on three switch ports (FastEthernet0/1, 0/2, 0/3), each with a different violation mode.

**Devices in the topology**
- **Switch0 (Cisco 2960)**
- **Hub0** (connected to multiple laptops and PC)
- **IP Phone0**
- **Several laptops (Laptop1, ROGUE, etc.)**

### STEP 1 — Interface Fa0/1 (Mode: Restrict)

Commands on Switch0:

```sh
enable
configure terminal
interface FastEthernet0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation restrict
```

<br>

Explanation:

| Setting                    | Description                                                      |
| -------------------------- | ---------------------------------------------------------------- |
| `switchport port-security` | Enables port security                                            |
| `maximum 3`                | Allows only 3 MAC addresses to be learned dynamically            |
| `mac-address sticky`       | Learns and stores MACs automatically                             |
| `violation restrict`       | Drops packets from unknown MACs and increments violation counter |


This interface is connected to **Hub0**, which will learn multiple MACs dynamically. When a **rogue device** connects and adds more than 3 MACs, this port will **restrict** it (drop packets but stay active).

<br>

### STEP 2 — Interface Fa0/2 (Mode: Shutdown)

Commands on Switch0:

```sh
interface FastEthernet0/2
 switchport mode access
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
```

> “shutdown” is actually the default violation mode. When violated → port goes err-disabled and must be manually recovered.

If this port exceeds 3 MACs, it will shut down (amber light).

<br>

### STEP 3 — Interface Fa0/3 (Mode: Protect, Static MAC)

Commands on Switch0

```sh
interface FastEthernet0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address 00E0.A3CE.3236
 switchport port-security violation protect
```

**Explanation**:
- Only one device (MAC: 00E0.A3CE.3236) can communicate on this port.
- If another laptop plugs in → traffic silently dropped (no counter, no shutdown).

<br>

### STEP 4 — Verification Commands

After configuration, check with:

```sh
show port-security
```

<br>

Example output:

```sh
Secure Port  MaxSecureAddr  CurrentAddr  SecurityViolation  Security Action
---------------------------------------------------------------------------
Fa0/1        3              3            5                  Restrict
Fa0/2        3              1            0                  Shutdown
Fa0/3        1              1            0                  Protect
```

<br>

Or detailed info per interface:

```sh
show port-security interface fa0/1
```

<br>

### STEP 5 — Test Scenario

**From Laptop1**: Ping other PCs (192.168.1.2, 192.168.1.3, etc.) → should work fine.

<br>

**Connect the “Rogue Laptop” to the Hub:**

1. Once connected, the hub and switch port (Fa0/1) will have >3 MAC addresses.
2. Port Fa0/1 is in **restrict** mode → it will drop traffic from the new (rogue) MAC.

Result:
- Rogue can ping 192.168.1.1 (since still under same port, initial ping OK)
- But cannot ping 192.168.1.4 (because of port-security restriction)

Check violations:

```sh
show port-security interface fa0/1
```

You should see:

```sh
Security Violation Count: >0
Security Action: Restrict
```

<br>

### STEP 6 — Recover from Shutdown (if triggered)

If port Fa0/2 goes into `err-disabled`:

```shk
interface FastEthernet0/2
 shutdown
 no shutdown
```

<br>

Then check:

```sh
show interface status
```

It should show **connected (green)** again.

<br>

## Summary of All Commands

### FastEthernet0/1 — Restrict

```sh
interface fa0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation restrict
```

<br>

### FastEthernet0/2 — Shutdown

```sh
interface fa0/2
 switchport mode access
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
```

<br>

### FastEthernet0/3 — Protect (Static)

```sh
interface fa0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address 00E0.A3CE.3236
 switchport port-security violation protect
```

<br>

## Optional Verification Table

| Port  | Max MACs | Mode   | Violation Action | Behavior                          |
| ----- | -------- | ------ | ---------------- | --------------------------------- |
| Fa0/1 | 3        | Sticky | Restrict         | Drops extra MAC, counts violation |
| Fa0/2 | 3        | Sticky | Shutdown         | Port disables on violation        |
| Fa0/3 | 1        | Static | Protect          | Drops silently, no counter        |

---