# Packet Tracer 8.2.2 - Lab 7 Static routes

## Network diagram

![network diagram](images/network-diagram.png)

## Lab instructions

1. Configure router's IP addresses : 
    - Atlanta Fa 0/0 : 10.112.8.1/30 
    - New York Fa 0/0 : 10.112.8.2/30 
    - New York Fa 0/1 : 10.114.65.129/30 
    - Chicage Fa 0/1 : 10.114.65.130/30 

2. Configure a static route on Chicago router for 10.112.8.0/30 network 
3. Configure a static route on Atanta router for 10.114.65.128/30 network 
4. From Atlanta, ping Chicago router to test your configuration.

<br>

## Solution

### Network Overview

```sh
Atlanta ───── 10.112.8.0/30 ───── New York ───── 10.114.65.128/30 ───── Chicago
```

| Router   | Interface | IP Address    | Subnet Mask     | Connected To   |
| -------- | --------- | ------------- | --------------- | -------------- |
| Atlanta  | Fa0/0     | 10.112.8.1    | 255.255.255.252 | New York Fa0/0 |
| New York | Fa0/0     | 10.112.8.2    | 255.255.255.252 | Atlanta        |
| New York | Fa0/1     | 10.114.65.129 | 255.255.255.252 | Chicago        |
| Chicago  | Fa0/1     | 10.114.65.130 | 255.255.255.252 | New York       |


<br>

### Step 1 — Configure Interface IPs

**Atlanta Router**

```sh
enable
configure terminal
interface FastEthernet0/0
 ip address 10.112.8.1 255.255.255.252
 no shutdown
exit
```

<br>

**New York Router**

```sh
enable
configure terminal
interface FastEthernet0/0
 ip address 10.112.8.2 255.255.255.252
 no shutdown
exit

interface FastEthernet0/1
 ip address 10.114.65.129 255.255.255.252
 no shutdown
exit
```

<br>

**Chicago Router**

```sh
enable
configure terminal
interface FastEthernet0/1
 ip address 10.114.65.130 255.255.255.252
 no shutdown
exit
```

Check connectivity so far:

- From New York, ping Atlanta (10.112.8.1)
- From New York, ping Chicago (10.114.65.130)

If both work → interfaces are configured correctly.

<br>

### Step 2 — Configure a Static Route on Chicago Router

Chicago needs a route to reach the **10.112.8.0/30** network (Atlanta ↔ New York link).

On **Chicago Router**:

```sh
ip route 10.112.8.0 255.255.255.252 10.114.65.129
```

> This means: “To reach 10.112.8.0/30, send traffic to next-hop 10.114.65.129 (New York).”

<br>

### Step 3 — Configure a Static Route on Atlanta Router

Atlanta needs a route to reach the **10.114.65.128/30** network (New York ↔ Chicago link).

On **Atlanta Router**:

ip route 10.114.65.128 255.255.255.252 10.112.8.2

> This means: “To reach 10.114.65.128/30, send traffic to next-hop 10.112.8.2 (New York).”

<br>

From **Atlanta Router**:

```sh
ping 10.114.65.130
```

If the routing is correct, the ping should **succeed**.

<br>

## Verification Commands

| Command                   | Purpose                        |
| ------------------------- | ------------------------------ |
| `show ip interface brief` | Check interface IPs and status |
| `show ip route`           | Verify static routes           |
| `ping <IP>`               | Test connectivity              |
| `traceroute <IP>`         | Show routing path              |

Example on Atlanta:

```sh
GATEWAY# show ip route
S    10.114.65.128 [1/0] via 10.112.8.2
C    10.112.8.0 is directly connected, FastEthernet0/0
```

<br>

## Summary of All Commands

Atlanta Router

```sh
interface Fa0/0
 ip address 10.112.8.1 255.255.255.252
 no shutdown
exit
ip route 10.114.65.128 255.255.255.252 10.112.8.2
```

<br>

New York Router

```sh
interface Fa0/0
 ip address 10.112.8.2 255.255.255.252
 no shutdown
exit
interface Fa0/1
 ip address 10.114.65.129 255.255.255.252
 no shutdown
exit
```

<br>

Chicago Router

```sh
interface Fa0/1
 ip address 10.114.65.130 255.255.255.252
 no shutdown
exit
ip route 10.112.8.0 255.255.255.252 10.114.65.129
```

---