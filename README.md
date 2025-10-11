# Packet Tracer 8.2.2 - Lab 6 Basic router setup

## Introduction
At the first boot of a Cisco ISR router, some basic configuration has to be performed to secure adminitrative access to the router. This lab will test your ability to configure the basic security settngs of a Cisco ISR router and help you to get ready for the router configuration simulation activities in the CCENT / ICND1 certification exam (Chapter 5.0 Infrastructure Maintenance of [Cisco Certified Entry Networking Technician (CCENT)](https://www.packettracernetwork.com/ccna-ccnp-preparation/ccentcertification.html) exam)

<br>

## Network diagram

![network diagram](images/network-diagram.png)

<br>

## Lab instructions
The aim of this lab is to test your ability to perform a basic router setup. You have 15 minutes to complete this simulation.

1. Configure the LAPTOP terminal software with the right console parameters.

2. Configure the router hostname to "GATEWAY"

3. Configure the enable password and secret to "cisco"

4. Configure password encryption on the router to secure stored passwords

5. Configure the console access :
    - Login : yes
    - Password : "cisco"
    - History : 10 commands
    - Logging synchronous
    - Timeout : 2 minutes 45 seconds.

<br>

## Solution

### Step 1 — Configure the Laptop Terminal

1. Click **Laptop** → **Desktop** → **Terminal**.
2. You’ll see the **Terminal Configuration** window (like in your image).
3. Set the parameters as follows:

| Setting             | Value |
| ------------------- | ----- |
| **Bits per second** | 9600  |
| **Data bits**       | 8     |
| **Parity**          | None  |
| **Stop bits**       | 1     |
| **Flow control**    | None  |


Then click OK — this opens a console session with the router.

<br>

### Step 2 — Configure the Router Hostname

Enter privileged mode and change the hostname:

```sh
Router> enable
Router# configure terminal
Router(config)# hostname GATEWAY
```

<br>

The prompt should now display:

```sh
GATEWAY(config)#
```

<br>

### Step 3 — Configure Enable Passwords

You will configure both the simple password and the encrypted secret:

```sh
GATEWAY(config)# enable password cisco
GATEWAY(config)# enable secret cisco
```

> The `enable secret` command automatically encrypts the password using MD5 and takes precedence over the plain `enable password`.

<br>

### Step 4 — Enable Password Encryption

This prevents plain-text passwords from being shown in the configuration file:

```sh
GATEWAY(config)# service password-encryption
```

<br>

### Step 5 — Configure Console Access

Set up secure console access with login, password, timeout, and history:

```sh
GATEWAY(config)# line console 0
GATEWAY(config-line)# password cisco
GATEWAY(config-line)# login
GATEWAY(config-line)# logging synchronous
GATEWAY(config-line)# exec-timeout 2 45
GATEWAY(config-line)# history size 10
GATEWAY(config-line)# exit
```

<br>

**Explanation**:

| Command               | Purpose                                                  |
| --------------------- | -------------------------------------------------------- |
| `password cisco`      | Sets console password                                    |
| `login`               | Enables password checking                                |
| `logging synchronous` | Prevents log messages from interrupting your typing      |
| `exec-timeout 2 45`   | Auto-logout after 2 minutes and 45 seconds of inactivity |
| `history size 10`     | Keeps the last 10 executed commands in history           |

<br>

### Step 6 — Verify and Save Configuration

Check your configuration:

```sh
show running-config
```

<br>

Then save it to NVRAM:

```sh
copy running-config startup-config
```

<br>

## Final Configuration Summary

| Step | Command                                              | Description                                   |
| ---- | ---------------------------------------------------- | --------------------------------------------- |
| 1    | Configure terminal to **9600 / 8 / None / 1 / None** | Enable console access                         |
| 2    | `hostname GATEWAY`                                   | Set router name                               |
| 3    | `enable secret cisco`                                | Set encrypted privileged password             |
| 4    | `service password-encryption`                        | Encrypt all passwords                         |
| 5    | `line console 0` …                                   | Configure console login, timeout, and logging |
| 6    | `copy run start`                                     | Save configuration permanently                |


<br>

## Quick Test

1. Close the terminal window, reconnect via console.
2. The router will now prompt for a **password** → type `cisco`.
3. Enter **enable mode** with `enable` → again use `cisco`.
4. Check the prompt — it should show:

    ```sh
    GATEWAY#
    ```

That means your lab is complete!

---