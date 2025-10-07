# Packet Tracer 8.2.2 - Lab 1 : Basic switch setup

## Introduction

A new switch just purchased from Cisco contains no default configuration.
You need to configure the switch with setup mode or from scratch using the command line interface (CLI) before connecting it in your network environment.

As a Cisco CCNA certified professional, it is very important to know the basic Cisco switch configuration commands to improve the performances and the security of the enterprise network.

<br>

## Network diagram

![network diagram](<images/network diagram.png>)

<br>

## Lab instructions

This lab will test your ability to configure basic settings on a cisco switch.

1. Use the local laptop connect to the switch console.

2. Configure Switch hostname as LOCAL-SWITCH

3. Configure the message of the day as "Unauthorized access is forbidden"

4. Configure the password for privileged mode access as "cisco". The password must be md5 encrypted

5. Configure password encryption on the switch using the global configuration command

6. Configure CONSOLE access with the following settings :
   - Login enabled
   - Password : ciscoconsole
   - History size : 15 commands
   - Timeout : 6'45''
   - Synchronous logging 

6. Configure TELNET access with the following settings :
   - Login enabled
   - Password : ciscotelnet
   - History size : 15 commands
   - Timeout : 8'20''
   - Synchronous logging 

7. Configure the IP address of the switch as 192.168.1.2/24 and it's default gateway IP (192.168.1.1).

8. Test telnet connectivity from the Remote Laptop using the telnet client.


Copyright : http://www.packettracernetwork.com

<br>

---

## Lab solution

### วัตถุประสงค์ของ Lab

ทดสอบความสามารถของคุณในการตั้งค่าพื้นฐานของ Cisco Switch เช่น
- การตั้งชื่อ (hostname)
- การใส่ข้อความเตือน (banner MOTD)
- การตั้งรหัสผ่านเข้าระบบ (console / telnet / privileged)
- การตั้งค่า IP สำหรับการบริหารจัดการ
- และการทดสอบเชื่อมต่อผ่าน Telnet

<br>

#### 1. ใช้เครื่อง Local Laptop เชื่อมต่อกับ Switch ผ่าน Console

ให้ใช้ สาย Console (สีน้ำเงิน) ต่อจากพอร์ต RS232 ของ Laptop → ไปยัง Console Port ของ Switch

คำแนะนำ
- คลิก Laptop → Tab “Desktop” → “Terminal”
- กด OK (ใช้ค่ามาตรฐาน: 9600 baud, 8 data bits, no parity, 1 stop bit, no flow control)
- จะเห็นหน้าจอ CLI ของ Switch

<br>

#### 2. ตั้งชื่อ Switch เป็น `LOCAL-SWITCH`

```sh
Switch> enable
Switch# configure terminal
Switch(config)# hostname LOCAL-SWITCH
```

> ทำให้แยกอุปกรณ์ง่ายขึ้นเวลาอยู่ในระบบจริง

<br>

#### 3. ตั้งข้อความเตือน (MOTD) ว่า “Unauthorized access is forbidden”

```sh
LOCAL-SWITCH(config)# banner motd #
Unauthorized access is forbidden#
```

> ข้อความนี้จะปรากฏทุกครั้งที่มีคนเข้าสวิตช์ เป็นข้อความเตือนด้านความปลอดภัย

<br>

#### 4. ตั้งรหัสผ่านโหมด Privileged (enable) เป็น “cisco” และต้องเข้ารหัสแบบ MD5

```sh
LOCAL-SWITCH(config)# enable secret cisco
```

> `enable secret` จะเข้ารหัสแบบ MD5 โดยอัตโนมัติ (ปลอดภัยกว่าคำสั่ง `enable password`)

<br>

#### 5. เข้ารหัสรหัสผ่านทั้งหมดใน config (เพื่อไม่ให้เห็น plain text)

```sh
LOCAL-SWITCH(config)# service password-encryption
```

> ป้องกันไม่ให้รหัสผ่านใน `running-config` แสดงเป็นข้อความธรรมดา

<br>

#### 6. ตั้งค่า Console Access

```sh
LOCAL-SWITCH(config)# line console 0
LOCAL-SWITCH(config-line)# password ciscoconsole
LOCAL-SWITCH(config-line)# login
LOCAL-SWITCH(config-line)# history size 15
LOCAL-SWITCH(config-line)# exec-timeout 6 45
LOCAL-SWITCH(config-line)# logging synchronous
```

| คำสั่ง                  | ความหมาย                                        |
| ----------------------- | ----------------------------------------------- |
| `login`                 | บังคับให้ใส่รหัสผ่านเมื่อเข้าผ่าน Console       |
| `password ciscoconsole` | ตั้งรหัสผ่าน Console                            |
| `exec-timeout 6 45`     | ออกอัตโนมัติหลังไม่มีการใช้งาน 6 นาที 45 วินาที |
| `logging synchronous`   | ป้องกันข้อความ log มารบกวนเวลาพิมพ์คำสั่ง       |
| `history size 15`       | เก็บประวัติคำสั่ง 15 บรรทัด                     |

<br>

#### 7. ตั้งค่า Telnet Access

```sh
LOCAL-SWITCH(config)# line vty 0 15
LOCAL-SWITCH(config-line)# password ciscotelnet
LOCAL-SWITCH(config-line)# login
LOCAL-SWITCH(config-line)# history size 15
LOCAL-SWITCH(config-line)# exec-timeout 8 20
LOCAL-SWITCH(config-line)# logging synchronous
```

| คำสั่ง                 | ความหมาย                                |
| ---------------------- | --------------------------------------- |
| `line vty 0 15`        | เปิดการตั้งค่าพอร์ตเสมือน Telnet (0–15) |
| `password ciscotelnet` | รหัสผ่านเวลาเชื่อมต่อ Telnet            |
| `login`                | เปิดให้ล็อกอินผ่านรหัสนี้ได้            |
| `exec-timeout 8 20`    | หมดเวลา 8 นาที 20 วินาที                |
| `logging synchronous`  | จัดข้อความ log ให้ตรงบรรทัด             |
| `history size 15`      | เก็บประวัติคำสั่ง                       |


<br>

#### 8. ตั้งค่า IP และ Gateway ให้กับ Switch

```sh
LOCAL-SWITCH(config)# interface vlan 1
LOCAL-SWITCH(config-if)# ip address 192.168.1.2 255.255.255.0
LOCAL-SWITCH(config-if)# no shutdown
LOCAL-SWITCH(config)# ip default-gateway 192.168.1.1
```

> IP นี้คือ “Management IP” ของสวิตช์ — ใช้สำหรับ Telnet เข้ามาบริหารจากระยะไกล และ Default Gateway ต้องเป็น Router (192.168.1.1)

<br>

#### 9. ทดสอบ Telnet จาก Remote Laptop

บน Remote Laptop → เปิด “Command Prompt”

```sh
telnet 192.168.1.2
```

หากทุกอย่างถูกต้อง จะเห็นข้อความ

```sh
Unauthorized access is forbidden
Password:
```

→ กรอกรหัส ciscotelnet แล้วเข้าสู่ Switch ได้สำเร็จ

---

### เคล็ดลับเพิ่มเติม

- ก่อน Telnet ต้องแน่ใจว่า VLAN1 “up” แล้ว (`no shutdown`)
- ถ้า Telnet ไม่เข้า ให้ตรวจ `ip default-gateway` และ `line vty`
- อย่าลืม `write memory` เพื่อบันทึก config
    ```sh
    LOCAL-SWITCH# copy running-config startup-config
    ```

---