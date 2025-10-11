# Packet Tracer 8.2.2 - Lab 3 : VLAN and VTP

## Nwtwork diagram

The aim of this lab is to test your ability to configure VLAN and VTP on a small network of 4 switches using Packet Tracer 8.2.

This lab will help you to prepare the VTP testlet and simlet questions of the Cisco CCNA exam.

![network-diagram](images/network-diagram.png)

<br>

## Lab instructions

1. Configure the VTP-SERVER switch as a VTP server

2. Connect to the 3 other switches and configure them as VTP clients. All links between swiches must be configured as trunk lines.

3. Configure VTP domain name as "TESTDOMAIN" and VTP password as "cisco"

4. Configure VLAN 10 with name "STUDENTS" and VLAN 50 with name "SERVERS"

5. Check propagation on all switches of the VTP domain.

<br>

## Solution

### STEP 1 — Configure VTP-SERVER

Go to VTP-SERVER CLI, then enter:

```sh
enable
configure terminal
vtp mode server
vtp domain TESTDOMAIN
vtp password cisco
```

<br>

Verify:

```sh
show vtp status
```

<br>

You should see:

```sh
VTP Operating Mode: Server
VTP Domain Name: TESTDOMAIN
VTP Password: cisco
```

<br>

#### STEP 2 — Configure VTP-CLIENT1, VTP-CLIENT2, VTP-CLIENT3

On each client switch:

```sh
enable
configure terminal
vtp mode client
vtp domain TESTDOMAIN
vtp password cisco
```

<br>

Verify:

```sh
show vtp status
```

<br>

Expected:

```sh
VTP Operating Mode: Client
VTP Domain Name: TESTDOMAIN
VTP Password: cisco
```

> Tip: Domain and password must match exactly (case-sensitive).

<br>

#### STEP 3 — Configure Trunk Links between all switches

Each connection between switches must be a **trunk port**.

Example — on **every switch**, configure both Gigabit ports:

```sh
configure terminal
interface range GigabitEthernet0/1 - 2
switchport mode trunk
no shutdown
exit
```

> Don’t use `switchport mode dynamic desirable` — the lab specifies **no negotiation**, so only `switchport mode trunk`.

<br>

Verify:

```sh
show interfaces trunk
```

You should see all Gi0/1 and Gi0/2 in `trunking` state.

<br>

#### STEP 4 — Create VLANs on the VTP-SERVER

Now, create VLAN 10 and VLAN 50 only on the server switch.

```sh
configure terminal
vlan 10
 name STUDENTS
exit
vlan 50
 name SERVERS
exit
```

<br>

Verify locally:

```sh
show vlan brief
```

<br>

Expected output:

```sh
VLAN Name      Status   Ports
1    default   active   Fa0/1 ...
10   STUDENTS  active
50   SERVERS   active
```

<br>

#### STEP 5 — Verify VTP Propagation

Now go to each client switch and type:

```sh
show vlan brief
```

<br>

Expected result (without manually creating VLANs):

```sh
VLAN Name      Status
1    default   active
10   STUDENTS  active
50   SERVERS   active
```

If VLANs 10 and 50 appear on all 3 clients → your **VTP is working correctly**.

<br>

#### STEP 6 — Optional Verification Commands

| Command                      | Description                            |
| ---------------------------- | -------------------------------------- |
| `show vtp status`            | Check mode, domain, revision number    |
| `show vlan brief`            | List all VLANs learned via VTP         |
| `show interfaces trunk`      | Verify trunk ports                     |
| `show interfaces switchport` | Confirm trunk/access mode of each port |

<br>

## Common Troubleshooting Tips

| Problem                   | Likely Cause                   | Fix                                       |
| ------------------------- | ------------------------------ | ----------------------------------------- |
| VLANs not propagating     | Trunks not configured properly | Run `show interfaces trunk` and fix ports |
| Client not learning VLANs | Wrong domain or password       | Re-enter `vtp domain` and `vtp password`  |
| Revision number mismatch  | Old config in client           | Use `delete flash:vlan.dat` then reload   |

<br>

## Summary of All Commands

On VTP-SERVER

```sh
enable
conf t
vtp mode server
vtp domain TESTDOMAIN
vtp password cisco
interface range gi1/1 - 2
 switchport mode trunk
vlan 10
 name STUDENTS
vlan 50
 name SERVERS
end
write memory
```

<br>

On VTP-CLIENT1, CLIENT2, CLIENT3

```sh
enable
conf t
vtp mode client
vtp domain TESTDOMAIN
vtp password cisco
interface range gi1/1 - 2
 switchport mode trunk
end
write memory
```

---