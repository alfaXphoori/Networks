# 📚 เอกสารประกอบการสอน (Lecture Notes - Full Detailed Edition)
## รายวิชาเครือข่ายคอมพิวเตอร์: Network Services & Security
**ครอบคลุมเนื้อหาบทที่ 13 - 17 (ACL, NAT, DHCP, DNS, VPN)**
> **คำแนะนำการใช้:** เอกสารนี้ใช้ประกอบการบรรยาย ซึ่งแต่ละบทมีทั้ง ทฤษฎี, ตัวอย่างการตั้งค่า, ตาราง, และคำสั่งตรวจสอบ ควรอ่านควบคู่กับการปฏิบัติใน EVE-NG Lab

---

## 🛡️ บทที่ 13: ACL (Access Control List) & Network Security

**🎯 จุดประสงค์การเรียนรู้:**
- เข้าใจบทบาทของ ACL ในการคัดกรอง Traffic บน Router
- แยกความแตกต่างระหว่าง Standard ACL และ Extended ACL ได้
- คำนวณ Wildcard Mask ได้อย่างถูกต้อง
- เขียนและ Apply ACL บน Interface ได้อย่างถูกต้องตามหลัก Best Practice

---

### 1. ACL คืออะไร และทำไมต้องใช้?

**ACL (Access Control List)** คือรายชื่อคำสั่ง (Ordered List of Statements) ที่ทำหน้าที่เป็น **"ยามรักษาความปลอดภัยที่ประตู"** ของ Router/Switch หน้าที่คือ **ตรวจสอบทุก Packet** ที่วิ่งผ่าน Interface แล้วตัดสินใจว่าจะ:
- **Permit:** อนุญาตให้ Packet วิ่งผ่านไปได้
- **Deny:** ปฏิเสธ ทิ้ง Packet ทำลายล้าง (Packet ถูกทิ้งเงียบๆ ไม่มีการแจ้งกลับ)

**สิ่งที่ ACL ทำได้:**
- กรอง Traffic จาก Network ภายนอกไม่ให้เข้ามาในองค์กร
- จำกัดสิทธิ PC ของนักศึกษาไม่ให้เข้า Server ของอาจารย์
- ควบคุมว่าใครถึงจะรีโมทเข้า Router ผ่าน Telnet/SSH ได้
- เป็นส่วนประกอบสำคัญของ NAT (ใช้ระบุกลุ่ม IP ที่จะแปลง)

```
[PC] ---> [Router Interface: ACL ตรวจที่นี่] ---> [Routing] ---> [ปลายทาง]
             ↑
        Permit = ผ่านไป
        Deny   = ทิ้งทำลาย
```

---

### 2. ชนิดของ ACL บน Cisco IOS

#### 2.1 Standard ACL (หมายเลข 1–99 และ 1300–1999)

| คุณสมบัติ | รายละเอียด |
|-----------|-----------|
| **ตรวจสอบ** | Source IP เท่านั้น |
| **หมายเลข** | 1–99, 1300–1999 |
| **ความยืดหยุ่น** | ต่ำ (บล็อกแบบเหมารวม) |
| **ควรวางไว้** | ใกล้ Destination |

```shell
! ตัวอย่าง: อนุญาตแค่วง 192.168.10.0/24 เท่านั้น บล็อกที่เหลือทั้งหมด
access-list 10 permit 192.168.10.0 0.0.0.255
! (ท้ายสุด implicit deny all ซ่อนอยู่เสมอ)

! นำไป Apply กับ Interface
interface GigabitEthernet0/1
 ip access-group 10 in
```

#### 2.2 Extended ACL (หมายเลข 100–199 และ 2000–2699)

| คุณสมบัติ | รายละเอียด |
|-----------|-----------|
| **ตรวจสอบ** | Source IP, Destination IP, Protocol (TCP/UDP/ICMP), Port Number |
| **หมายเลข** | 100–199, 2000–2699 |
| **ความยืดหยุ่น** | สูงมาก (เจาะจงได้ถึงระดับ Port) |
| **ควรวางไว้** | ใกล้ Source |

**รูปแบบคำสั่ง Extended ACL:**
```
access-list [หมายเลข] [permit/deny] [protocol] [source] [wildcard] [destination] [wildcard] [eq port]
```

```shell
! ตัวอย่างที่ 1: บล็อก PC วง 192.168.50.0/24 ไม่ให้ PING Web Server 192.168.100.10
access-list 101 deny icmp 192.168.50.0 0.0.0.255 host 192.168.100.10

! ตัวอย่างที่ 2: อนุญาตให้ทุกคน เข้าเว็บ (HTTP port 80) ของ Web Server ได้
access-list 101 permit tcp any host 192.168.100.10 eq 80

! ตัวอย่างที่ 3: อนุญาต Traffic อื่นๆ ทั้งหมด
access-list 101 permit ip any any

! Apply เข้า Interface ฝั่งขาเข้าจาก PC
interface GigabitEthernet0/0
 ip access-group 101 in
```

**Port Numbers ที่ควรจำ:**

| Service | Protocol | Port |
|---------|----------|------|
| HTTP (เว็บ) | TCP | 80 |
| HTTPS (เว็บเข้ารหัส) | TCP | 443 |
| FTP | TCP | 21 |
| SSH | TCP | 22 |
| Telnet | TCP | 23 |
| DNS | UDP/TCP | 53 |
| DHCP Server | UDP | 67 |
| DHCP Client | UDP | 68 |(
| SMTP (ส่งอีเมล) | TCP | 25 |

---

### 3. Wildcard Mask คืออะไร?

Wildcard Mask คือ **ส่วนกลับ (Complement) ของ Subnet Mask** ใช้ระบุว่า Bit ไหนต้องตรงกันเป๊ะ Bit ไหนเป็นอะไรก็ได้:
- **Bit = 0:** บิตนี้ต้อง **ตรงกัน** กับ IP ที่ระบุ
- **Bit = 1:** บิตนี้ **ข้ามได้** เป็นอะไรก็ได้ (Don't Care)

**วิธีคำนวณ:** `Wildcard Mask = 255.255.255.255 - Subnet Mask`

| Subnet Mask | Wildcard Mask | ความหมาย |
|-------------|---------------|----------|
| 255.255.255.255 | 0.0.0.0 | ระบุ Host เดี่ยวๆ ตัวเดียว (ใช้ `host` แทนก็ได้) |
| 255.255.255.0 | 0.0.0.255 | ทั้ง Network /24 |
| 255.255.0.0 | 0.0.255.255 | ทั้ง Network /16 |
| 0.0.0.0 | 255.255.255.255 | ทุก IP บนโลก (ใช้ `any` แทนก็ได้) |

```shell
! สามแบบที่เขียนได้เหมือนกัน
access-list 10 permit host 192.168.1.1
access-list 10 permit 192.168.1.1 0.0.0.0

access-list 10 permit any
access-list 10 permit 0.0.0.0 255.255.255.255
```

---

### 4. กฎเหล็ก 5 ข้อ (ACL Best Practices)

1. **Implicit Deny All (กฎซ่อน):**
   Router จะซ่อน `deny ip any any` เอาไว้ที่บรรทัดสุดท้ายเสมอ ถ้าไม่มีการ `permit` traffic ที่ต้องการผ่าน มันจะโดนบล็อกหมดทุกอย่าง ดังนั้นต้องระวัง!

2. **Top-Down Processing (อ่านจากบนลงล่าง):**
   Router ตรวจทีละกฎจากบนลงล่าง **เจอกฎแรกที่ Match จะ Execute ทันทีแล้วหยุดอ่าน** ดังนั้น **กฎที่เจาะจงที่สุด** ต้องนำขึ้นไปไว้บนสุด

3. **Standard ACL วางใกล้ Destination:**
   เพราะ Standard ACL ดูแค่ Source ถ้าวางใกล้ Source มันจะบล็อก Traffic นั้นออกจาก Router ทุกทิศทาง ไม่ใช่แค่ไปที่เดียว

4. **Extended ACL วางใกล้ Source:**
   เพราะสามารถระบุปลายทางได้ การวางใกล้ Source ทำให้ Traffic ที่โดน Deny หยุดตั้งแต่ต้นทาง ไม่เปลือง Bandwidth วิ่งไปทั่วเครือข่ายก่อน

5. **ACL 1 ตัวต่อ 1 Interface ต่อ 1 ทิศทาง:**
   Interface หนึ่งๆ Accept ได้แค่ **1 ACL ต่อ Direction (in/out)** ถ้า Apply ซ้ำจะเขียนทับ (ไม่รวมกัน)

---

### 5. Named ACL (การตั้งชื่อ ACL)

แทนที่จะใช้ตัวเลข สามารถตั้งชื่อให้อ่านรู้เรื่องได้ และยังแก้ไข/ลบทีละ Entry ได้ด้วย

```shell
! สร้าง Named Extended ACL
ip access-list extended BLOCK_STUDENT_HTTP
 deny   tcp 192.168.50.0 0.0.0.255 host 192.168.100.10 eq 80
 permit ip any any

! Apply กับ Interface
interface GigabitEthernet0/0
 ip access-group BLOCK_STUDENT_HTTP in
```

---

### 6. คำสั่งตรวจสอบ (Verification)

```shell
! ดู ACL ทั้งหมดที่มีใน Router
show access-lists

! ดู ACL หมายเลข 101 เท่านั้น
show access-lists 101

! ดูว่า Interface ไหน Apply ACL อะไรอยู่
show ip interface GigabitEthernet0/0

! ล้างค่า Hit Counter (ตัวนับจำนวน Packet ที่ Match)
clear ip access-list counters
```

**ผลลัพธ์ตัวอย่าง:**
```
Router# show access-lists 101
Extended IP access list 101
    10 deny icmp 192.168.50.0 0.0.0.255 host 192.168.100.10 (25 matches)
    20 permit tcp any host 192.168.100.10 eq 80 (312 matches)
    30 permit ip any any (1048 matches)
```

---

## 🌐 บทที่ 14: NAT (Network Address Translation)

**🎯 จุดประสงค์การเรียนรู้:**
- เข้าใจสาเหตุที่ต้องมี NAT และปัญหา IPv4 Exhaustion
- แยกประเภทของ NAT (Static, Dynamic, PAT) และรู้ว่าใช้เมื่อไหร่
- คอนฟิก NAT Overload (PAT) และ Port Forwarding บน Cisco Router ได้
- อ่าน NAT Translation Table เพื่อ Debug ปัญหาได้

---

### 1. ทำไมถึงต้องมี NAT?

**ปัญหา IPv4 Exhaustion:**
IPv4 Address มีทั้งหมด $2^{32}$ = ประมาณ **4.29 พันล้านหมายเลข** แต่จำนวนอุปกรณ์บนโลก (สมาร์ทโฟน, คอมพิวเตอร์, IoT) มีมากกว่าหลายเท่า IANA (Internet Assigned Numbers Authority) ประกาศ IPv4 Pool หมดในปี ค.ศ. 2011

**ทางออกด้วย RFC 1918 (Private IP Ranges):**
กำหนดให้มีช่วง IP ที่ใช้ภายในองค์กรเท่านั้น ห้ามนำไป Route บนอินเทอร์เน็ตโดยตรง:

| Class | Private IP Range | Subnet Mask | จำนวน IP ที่ใช้ได้ |
|-------|-----------------|-------------|-------------------|
| A | 10.0.0.0 – 10.255.255.255 | /8 | 16,777,214 |
| B | 172.16.0.0 – 172.31.255.255 | /12 | 1,048,574 |
| C | 192.168.0.0 – 192.168.255.255 | /16 | 65,534 |

**NAT** คือกลไก "แปลงร่าง" ที่ทำให้ Private IP ออกไปท่องอินเทอร์เน็ตได้ โดยการเปลี่ยนหัว Packet ให้เป็น Public IP ก่อนส่งออก

---

### 2. คำศัพท์ NAT (NAT Terminology)

ต้องเข้าใจ 4 คำนี้ให้ได้ก่อน:

| คำศัพท์ | ความหมาย | ตัวอย่าง |
|---------|----------|---------|
| **Inside Local** | IP ของเครื่องในแลนของเรา (Private IP จริงๆ) | 192.168.1.10 |
| **Inside Global** | IP สาธารณะที่เป็นตัวแทน Inside เวลาออกอินเทอร์เน็ต | 203.0.113.5 |
| **Outside Local** | IP ของปลายทางบนอินเทอร์เน็ต (มองจากข้างใน) | 8.8.8.8 |
| **Outside Global** | IP ของปลายทางจริงๆ (มองจากข้างนอก) | 8.8.8.8 |

> **จำง่ายๆ:** Inside = อุปกรณ์ฝั่งเรา, Outside = ปลายทางบนเน็ต, Local = IP ที่เห็นจากฝั่งใน, Global = IP ที่เห็นจากฝั่งนอก

```
[PC: 192.168.1.10]                     [Internet: 8.8.8.8]
     Inside Local                          Outside Global
         |                                      |
      [Router NAT]
    Inside Global: 203.0.113.5
```

---

### 3. ชนิดของ NAT อย่างละเอียด

#### 3.1 Static NAT (1:1 Mapping)
แมพ Private IP 1 เบอร์ ↔ Public IP 1 เบอร์ **แบบถาวร**

**เหมาะสำหรับ:** Web Server, Mail Server, CCTV ที่ต้องการให้คนนอกเข้าถึงได้ตลอดเวลา

```shell
! กำหนดว่า IP 192.168.10.10 จะแปลงเป็น 203.0.113.10 เสมอ
ip nat inside source static 192.168.10.10 203.0.113.10

interface GigabitEthernet0/0
 description WAN
 ip nat outside

interface GigabitEthernet0/1
 description LAN
 ip nat inside
```

#### 3.2 Dynamic NAT (Pool Mapping)
แมพ Private IP กลุ่มหนึ่ง เข้ากับ Pool ของ Public IP

**เหมาะสำหรับ:** มี Public IP หลายเบอร์ และต้องการแจกแบบ First-Come First-Served

```shell
! กำหนด Pool ของ Public IP
ip nat pool PUBLIC_POOL 203.0.113.1 203.0.113.10 netmask 255.255.255.0

! กำหนด ACL ระบุว่า IP ภายในกลุ่มไหนจะแปลง
access-list 1 permit 192.168.0.0 0.0.255.255

! เชื่อม ACL กับ Pool
ip nat inside source list 1 pool PUBLIC_POOL
```

#### 3.3 PAT / NAT Overload (Many:1 Mapping)
ใช้ Public IP **เพียงเบอร์เดียว** แต่รองรับเครื่องภายในหลายพันเครื่อง โดยใช้ **Port Number** แยกแยะ Session

**เหมาะสำหรับ:** องค์กรทั่วไป, บ้านพักที่ได้ Public IP จาก ISP เพียงเบอร์เดียว (ใช้กับงาน Lab มากที่สุด)

```shell
! ACL ระบุ IP ภายในที่จะแปลง
access-list 1 permit 192.168.0.0 0.0.255.255

! แปลงโดยใช้ IP ของ Interface ปลาย WAN
ip nat inside source list 1 interface GigabitEthernet0/0 overload

interface GigabitEthernet0/0
 ip nat outside

interface GigabitEthernet0/1
 ip nat inside
```

**กลไกภายใน PAT Table:**

```
┌─────────────────────────────────────────────────────┐
│               PAT Translation Table                  │
├──────────────────────┬──────────────────────────────┤
│   Inside Local       │   Inside Global               │
├──────────────────────┼──────────────────────────────┤
│ 192.168.1.10:54321   │ 203.0.113.5:54321             │
│ 192.168.1.11:54322   │ 203.0.113.5:54322             │
│ 192.168.1.12:60000   │ 203.0.113.5:60000             │
└──────────────────────┴──────────────────────────────┘
```

#### 3.4 Port Forwarding (Static NAT ระดับ Port)
ให้คนข้างนอกเข้าถึง Server ข้างใน ผ่าน Port ที่กำหนด

```shell
! Port Forward: คนเปิด http://203.0.113.5:80 จะถูกส่งไปที่ Web Server 192.168.20.10:80
ip nat inside source static tcp 192.168.20.10 80 interface GigabitEthernet0/0 80

! Port Forward FTP ด้วย
ip nat inside source static tcp 192.168.20.20 21 interface GigabitEthernet0/0 21
```

---

### 4. ตัวอย่างคอนฟิก R1 แบบสมบูรณ์ (PAT + Port Forward)

```shell
enable
configure terminal

! Interface ฝั่ง WAN
interface GigabitEthernet0/0
 description WAN to ISP
 ip address dhcp
 ip nat outside
 no shutdown

! Interface ฝั่ง LAN
interface GigabitEthernet0/1
 description LAN
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 no shutdown

! ACL สำหรับ NAT (อนุญาต Private IP ทั้งหมด)
access-list 1 permit 192.168.0.0 0.0.255.255

! คอนฟิก PAT
ip nat inside source list 1 interface GigabitEthernet0/0 overload

! Port Forward Web Server
ip nat inside source static tcp 192.168.1.10 80 interface GigabitEthernet0/0 80

end
write memory
```

---

### 5. คำสั่งตรวจสอบ (Verification Commands)

```shell
! ดู NAT Translation Table ทั้งหมด
show ip nat translations

! ดูสรุปสถิติ NAT
show ip nat statistics

! ล้าง NAT Table ทิ้งทั้งหมด (ใช้ตอน Troubleshoot)
clear ip nat translation *

! Debug NAT แบบ Real-time (กด Ctrl+C หยุด)
debug ip nat
```

**ผลลัพธ์ตัวอย่าง:**
```
Router# show ip nat translations
Pro  Inside global       Inside local        Outside local       Outside global
tcp  203.0.113.5:80      192.168.1.10:80     ---                 ---
udp  203.0.113.5:54321   192.168.1.11:54321  8.8.8.8:53          8.8.8.8:53
tcp  203.0.113.5:60123   192.168.1.12:60123  142.250.190.46:80   142.250.190.46:80
```

---

## 🖧 บทที่ 15: DHCP (Dynamic Host Configuration Protocol)

**🎯 จุดประสงค์การเรียนรู้:**
- เข้าใจว่า DHCP แจก IP ให้ Client อย่างไร (กระบวนการ DORA)
- รู้จัก Lease Time และการต่ออายุสัญญา
- คอนฟิก DHCP Server ทั้งบน Cisco Router และ Linux (isc-dhcp-server) ได้
- คอนฟิก DHCP Relay (IP Helper) เพื่อส่ง DHCP ข้ามเครือข่ายได้

---

### 1. DHCP ทำหน้าที่อะไร?

ในองค์กรที่มีคอมพิวเตอร์ 500 เครื่อง หากต้องตั้งค่า IP Address, Subnet Mask, Gateway, DNS ด้วยมือทุกเครื่อง จะเกิดปัญหาสองอย่าง:
1. **เสียเวลา** มหาศาล
2. **IP Conflict** เมื่อพิมพ์ IP ซ้ำกัน จะทำให้ทั้งสองเครื่องออกเน็ตไม่ได้

DHCP (ทำงานใน Application Layer บนโปรโตคอล UDP) แก้ปัญหานี้โดยมีเซิร์ฟเวอร์กลางคอยแจก IP อัตโนมัติ:
- **UDP Port 67:** ฝั่ง DHCP Server
- **UDP Port 68:** ฝั่ง DHCP Client

---

### 2. กระบวนการ DORA (4 ขั้นตอน) อย่างละเอียด

```
CLIENT                              SERVER
  |                                   |
  |------- DHCP Discover (Broadcast)->|  Step 1: ขอ Server
  |                                   |
  |<------ DHCP Offer (Broadcast) ----|  Step 2: เสนอ IP
  |                                   |
  |------- DHCP Request (Broadcast)-->|  Step 3: ยืนยันขอเช่า
  |                                   |
  |<------ DHCP Ack (Unicast) --------|  Step 4: ยืนยันให้เช่า
  |                                   |
```

**ขั้นตอนที่ 1 — DHCP Discover:**
- Client เพิ่งเปิดเครื่อง/ต่อเน็ต ยังไม่มี IP
- ส่ง Broadcast `255.255.255.255` ออกไป พร้อม Source IP `0.0.0.0` (ยังไม่รู้ว่าตัวเองคือใคร)
- ข้อความ: *"ใครเป็น DHCP Server บ้าง? ฉันต้องการ IP!"*

**ขั้นตอนที่ 2 — DHCP Offer:**
- Server เห็น Discover แล้วจอง IP ไว้ชั่วคราว มาตอบกลับ
- ส่ง Packet บอก: IP ที่เสนอ, Subnet, Gateway, DNS, Lease Time
- ถ้ามีหลาย Server หลาย Offer จะมา Client รับอันแรกที่ได้รับ

**ขั้นตอนที่ 3 — DHCP Request:**
- Client เลือก Offer ที่ตอบกลับมาอันแรก แล้วประกาศ Broadcast ให้ทุกคนรู้
- บอก Server ที่ชนะว่า "ฉันขอเช่า IP จากคุณ"
- บอก Server อื่นๆ ว่า "ขอบคุณนะ แต่ฉันเลือกคนอื่นแล้ว ปล่อย IP ที่จองไว้ด้วย"

**ขั้นตอนที่ 4 — DHCP Ack:**
- Server บันทึกข้อมูลลง Lease Table (IP + MAC Address + หมดอายุเมื่อไหร่)
- ส่ง Ack ยืนยัน Client เริ่มใช้ IP ได้ทันที

---

### 3. Lease Time และการต่ออายุ

เวลาที่ IP ถูกให้ยืมไม่ใช่ตลอดไป มีลักษณะเป็น "สัญญาเช่า":

```
|---T1 (50%)---|---T2 (87.5%)---|--- หมดอายุ ---|
    ↑                ↑
    ขอต่อกับ         ขอต่อกับ
    Server เดิม      Server ใหม่ใดก็ได้
    (Unicast)        (Broadcast)
```

- **T1 (50% ของ Lease):** Client Unicast ไปหา Server เดิมเพื่อขอต่ออายุ
- **T2 (87.5% ของ Lease):** ถ้าจนถึง T2 แล้ว Server เดิมยังไม่ตอบ Client จะ Broadcast ขอต่ออายุจาก Server ตัวใดก็ได้
- **หมดอายุ:** ถ้าไม่ได้ IP ใหม่ เครื่องจะหยุดใชัIP เก่าทันที (ออกเน็ตไม่ได้)

---

### 4. ตัวอย่างคอนฟิก DHCP Server แบบต่างๆ

#### 4.1 DHCP บน Cisco Router

```shell
enable
configure terminal

! ไม่แจก IP 10 เบอร์แรก (สำรองไว้ให้ Server, Printer, อุปกรณ์ Fixed)
ip dhcp excluded-address 192.168.10.1 192.168.10.10

! สร้าง Pool สำหรับวง 192.168.10.0/24
ip dhcp pool LAN_POOL
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8 8.8.4.4
 lease 7                          ! อายุสัญญาเช่า 7 วัน
exit

end
write memory
```

#### 4.2 DHCP บน Linux (isc-dhcp-server)

**ไฟล์คอนฟิก:** `/etc/dhcp/dhcpd.conf`
```
# ตั้ง Default Lease Time
default-lease-time 86400;     # 1 วัน (หน่วย: วินาที)
max-lease-time 604800;        # 7 วัน

# Subnet Statement ของ DHCP Server เอง (ต้องมีแม้ไม่แจก)
subnet 192.168.100.0 netmask 255.255.255.0 {}

# Pool สำหรับ Site-2 LAN (192.168.20.0/24)
subnet 192.168.20.0 netmask 255.255.255.0 {
  range 192.168.20.50 192.168.20.200;
  option routers 192.168.20.1;
  option domain-name-servers 8.8.8.8, 8.8.4.4;
  option domain-name "lab.local";
}
```

**ไฟล์กำหนด Interface:** `/etc/default/isc-dhcp-server`
```
INTERFACESv4="ens3"
```

```shell
# เริ่ม/รีสตาร์ท Service
systemctl restart isc-dhcp-server
systemctl enable  isc-dhcp-server

# ตรวจสอบ Lease ที่แจกออกไปแล้ว
cat /var/lib/dhcp/dhcpd.leases
```

---

### 5. DHCP Relay (IP Helper-Address) อย่างละเอียด

**ปัญหา:** Router บล็อก Broadcast โดยอัตโนมัติ ทำให้ DHCP Discover จาก Client ที่อยู่คนละ Subnet ไม่สามารถข้ามไปหา Server ได้

```
[PC -Discover→ Broadcast]---[ROUTER]---X--- Server ไม่ได้รับ
                                ↑
                     Router บล็อก Broadcast ไว้
```

**ทางแก้ (IP Helper-Address):**
```shell
! บน Router ฝั่งที่ติดกับ Client (LAN Interface)
interface GigabitEthernet0/1
 description LAN Side
 ip address 192.168.20.1 255.255.255.0
 ip helper-address 192.168.100.10    ! IP ของ DHCP Server
 no shutdown
```

**กระบวนการหลังใส่ IP Helper:**
```
[PC] ---Broadcast---> [Router Gi0/1]
                           |
                    Router เปลี่ยนเป็น Unicast
                           |
                           ↓
                    [DHCP Server: 192.168.100.10]
                           |
                    ส่ง DHCP Offer กลับ
                           ↓
                    [Router] ---ส่งต่อไปหา PC---
```

---

### 6. คำสั่งตรวจสอบ DHCP (Cisco)

```shell
! ดูรายการ IP ที่แจกออกไปทั้งหมด (Lease Binding)
show ip dhcp binding

! ดูรายละเอียดของ Pool ที่ตั้งค่าไว้
show ip dhcp pool

! ดูสถิติ (จำนวน Lease, Collision, เฉพาะข้อผิดพลาด)
show ip dhcp statistics

! ดูว่ามี IP ไหนถูก Conflict บ้าง
show ip dhcp conflict
```

**ผลลัพธ์ตัวอย่าง:**
```
Router# show ip dhcp binding
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.10.11    0100.5056.ab12.34       Mar 13 2026 10:20 AM    Automatic
192.168.10.12    0100.5056.ab56.78       Mar 13 2026 11:05 AM    Automatic
```

---

## 🌍 บทที่ 16: DNS (Domain Name System)

**🎯 จุดประสงค์การเรียนรู้:**
- เข้าใจว่า DNS แปลงชื่อเป็น IP อย่างไร (DNS Resolution Process)
- อธิบายลำดับชั้นของ DNS Hierarchy ได้
- รู้จัก DNS Record ทุกประเภทที่สำคัญ
- คอนฟิก BIND9 บน Linux เป็น DNS Server สำหรับ Lab ได้

---

### 1. DNS คืออะไร และทำไมถึงต้องมี?

คอมพิวเตอร์คุยกันด้วย **IP Address** เช่น `142.250.190.46` แต่มนุษย์จำชื่อ **Domain Name** เช่น `www.google.com` ได้ง่ายกว่า DNS (Domain Name System) คือระบบฐานข้อมูลแบบกระจาย ทำหน้าที่แปลระหว่างสองโลกนี้ ทำงานบน:
- **UDP Port 53:** สำหรับ Query/Response ขนาดเล็ก (ส่วนใหญ่)
- **TCP Port 53:** สำหรับ Zone Transfer หรือ Response ขนาดใหญ่เกิน 512 bytes

---

### 2. โครงสร้างของ DNS (DNS Hierarchy)

ระบบ DNS ไม่ได้ใช้ Server เดียวตัวใหญ่ แต่กระจายแบบ Inverted Tree ดังนี้:

```
                         . (Root)
                    /         \
               .com           .th
             /       \           \
         google.com  ksu.ac.          ac.th
         /                              \
     www.google.com                  ksu.ac.th
```

**ระดับที่ 1 — Root DNS (`.`):**
- มี 13 ชุด Root Server หลักทั่วโลก (ตั้งชื่อ A-M)
- ไม่รู้ IP ของทุก Domain แต่รู้ว่า TLD ไหนอยู่กับใคร

**ระดับที่ 2 — TLD Servers (Top-Level Domain):**
- `.com` = Verisign
- `.th` = THNIC
- `.org`, `.net`, `.edu`, `.gov`, `.io` ฯลฯ

**ระดับที่ 3 — Authoritative Name Servers:**
- Server ที่องค์กรนั้นๆ ดูแลเองหรือซื้อ Hosting
- คือคำตอบสุดท้ายที่ "รู้จริง" ว่า `www.google.com = 142.250.190.46`

**ระดับที่ 0 — Local DNS Resolver / Recursive Resolver:**
- ปกติได้มาจาก DHCP (เช่น 8.8.8.8, 1.1.1.1)
- เป็นด่านแรกที่คอมพิวเตอร์เราถามก่อน มี **Cache** เก็บผลไว้ชั่วคราวด้วย

---

### 3. DNS Resolution Process ทีละขั้น

เมื่อคุณพิมพ์ `www.google.com` ในเบราว์เซอร์ กระบวนการต่อไปนี้เกิดขึ้น:

```
[Your PC] ---(1: www.google.com?)--->  [Local Resolver 8.8.8.8]
                                              |
                              (2: Cache Miss → ถาม Root)
                                              |
                                       [Root Server]
                                       ตอบว่า: ".com อยู่กับ X"
                                              |
                              (3: ถาม TLD .com Server)
                                              |
                                       [TLD .com Server]
                                       ตอบว่า: "google.com อยู่กับ Y"
                                              |
                              (4: ถาม Authoritative Server)
                                              |
                                [Google Authoritative NS]
                                ตอบว่า: "www.google.com = 142.250.190.46"
                                              |
                              (5: Resolver บันทึก Cache + ตอบกลับ)
                                              |
[Your PC] <---  IP: 142.250.190.46 ----------
   |
   └──> เปิด TCP Connection ไปที่ 142.250.190.46:80 ต่อเลย
```

> **TTL (Time To Live):** ค่าในนาที/วินาทีที่บอก Resolver ว่า Cache ข้อมูลนี้ได้นานแค่ไหน หลังจากนั้นต้องไปถามใหม่

---

### 4. DNS Records สำคัญทั้งหมด

| Record Type | หน้าที่ | ตัวอย่าง |
|-------------|---------|---------|
| **A** | ชี้ชื่อ Domain → IPv4 | `www.ksu.ac.th → 203.159.x.x` |
| **AAAA** | ชี้ชื่อ Domain → IPv6 | `www.google.com → 2607:f8b0:...` |
| **CNAME** | นามแฝง ชี้ชื่อ → ชื่อ | `mail.ksu.ac.th → mx.ksu.ac.th` |
| **MX** | ระบุ Mail Server | `ksu.ac.th MX → aspmx.l.google.com` |
| **PTR** | Reverse DNS ชี้ IP → ชื่อ | `203.159.1.10 → server.ksu.ac.th` |
| **NS** | ระบุว่า Name Server ของ Zone นี้คืออะไร | `ksu.ac.th NS → ns1.ksu.ac.th` |
| **SOA** | Start of Authority ข้อมูลหลักของ Zone | Admin Email, Serial Number |
| **TXT** | ข้อมูลข้อความทั่วไป | SPF Record, DKIM สำหรับอีเมล |

---

### 5. ตัวอย่างคอนฟิก BIND9 บน Linux

**ติดตั้ง:**
```shell
apt-get update
apt-get install -y bind9 bind9utils
```

**ไฟล์หลัก:** `/etc/bind/named.conf.local`
```
zone "lab.local" {
    type master;
    file "/etc/bind/zones/db.lab.local";
};

zone "20.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/db.192.168.20";
};
```

**Zone File Forward (ชื่อ → IP):** `/etc/bind/zones/db.lab.local`
```
$TTL    604800
@       IN      SOA     ns1.lab.local. admin.lab.local. (
                              2026030601     ; Serial
                              604800         ; Refresh
                              86400          ; Retry
                              2419200        ; Expire
                              604800 )       ; Negative TTL

; Name Servers
@       IN      NS      ns1.lab.local.

; A Records
ns1     IN      A       192.168.100.10
web     IN      A       192.168.20.10
ftp     IN      A       192.168.20.20

; CNAME
www     IN      CNAME   web
```

**Zone File Reverse (IP → ชื่อ):** `/etc/bind/zones/db.192.168.20`
```
$TTL    604800
@       IN      SOA     ns1.lab.local. admin.lab.local. (
                              2026030601 604800 86400 2419200 604800 )

@       IN      NS      ns1.lab.local.

; PTR Records
10      IN      PTR     web.lab.local.
20      IN      PTR     ftp.lab.local.
```

```shell
# ตรวจสอบ Config / Zone ก่อน Restart
named-checkconf
named-checkzone lab.local /etc/bind/zones/db.lab.local

# เริ่มใช้งาน
systemctl restart bind9
systemctl enable  bind9
```

---

### 6. คำสั่งทดสอบ DNS

```shell
# ทดสอบ DNS Resolution (คำสั่งง่ายๆ)
nslookup web.lab.local 192.168.100.10

# ทดสอบแบบละเอียด (แสดง TTL, Record Type ฯลฯ)
dig @192.168.100.10 web.lab.local

# ทดสอบ Reverse DNS
dig @192.168.100.10 -x 192.168.20.10

# ดู DNS Cache บนเครื่อง Windows
ipconfig /displaydns

# ล้าง DNS Cache บนเครื่อง Windows
ipconfig /flushdns
```

**ผลลัพธ์ตัวอย่าง:**
```
$ dig @192.168.100.10 web.lab.local

;; ANSWER SECTION:
web.lab.local.  604800  IN  A  192.168.20.10

;; Query time: 1 msec
;; SERVER: 192.168.100.10#53(192.168.100.10)
```

---

## 🔒 บทที่ 17: VPN (Virtual Private Network)

**🎯 จุดประสงค์การเรียนรู้:**
- เข้าใจหลักการอุโมงค์เสมือน (Tunneling) และการเข้ารหัส (Encryption)
- อธิบาย CIA Triad และความสำคัญในระบบ VPN ได้
- แยกความแตกต่างระหว่าง Site-to-Site VPN และ Remote Access VPN ได้
- เข้าใจกระบวนการ IPsec IKE Phase 1 และ Phase 2 เบื้องต้น
- คอนฟิก GRE Tunnel และ IPsec Site-to-Site VPN บน Cisco Router ได้

---

### 1. ทำไมต้องใช้ VPN?

**ปัญหาก่อนมี VPN:**
- บริษัทมีสาขาต่างจังหวัด ต้องการเชื่อม Network เข้าหากัน
- **ทางเลือกที่ 1 (แพงมาก):** เช่าสาย Leased Line / MPLS ส่วนตัว → ค่าใช้จ่ายหลักแสนบาทต่อเดือน
- **ทางเลือกที่ 2 (VPN):** ใช้อินเทอร์เน็ตสาธารณะ + เข้ารหัสข้อมูล → ค่าใช้จ่ายลดลงมหาศาล

**หลักการ Tunneling:**
VPN ทำการ **"ห่อหุ้มแพ็กเก็ตต้นฉบับ"** ด้วยหัว (Header) ชั้นใหม่ แล้วเข้ารหัส Data ข้างใน ก่อนส่งผ่านอินเทอร์เน็ต เปรียบเหมือนนำจดหมายลับใส่ซองธรรมดา แล้วส่งทางไปรษณีย์ปกติ แม้คนดักจับซองได้ แต่ไม่สามารถอ่านข้อมูลข้างในได้

```
ก่อน VPN:
[Original Packet: SRC=192.168.1.10 → DST=10.0.0.20]

หลัง VPN Encapsulation:
[Outer Header: SRC=203.0.113.1 → DST=198.51.100.1] [ENCRYPTED: Original Packet]
 ↑ IP สาธารณะของ Router ทั้งสองฝั่ง           ↑ ข้อมูลจริงถูกเข้ารหัสมิดชิด
```

---

### 2. CIA Triad: สามเสาหลักของความปลอดภัย VPN

| เสาหลัก | ความหมาย | เทคโนโลยีที่ใช้ |
|---------|----------|----------------|
| **C - Confidentiality (ความลับ)** | Data ข้างในต้องอ่านไม่ออกหากถูกดักจับ | AES-128, AES-256, 3DES |
| **I - Integrity (ความสมบูรณ์)** | Data ต้องไม่ถูกแก้ไขระหว่างทาง | HMAC-SHA1, HMAC-SHA256 |
| **A - Authentication (การยืนยันตัวตน)** | ต้องมั่นใจว่าคุยกับตัวจริง ไม่ใช่แฮกเกอร์แอบอ้าง | Pre-Shared Key, RSA Certificate |

---

### 3. ประเภทของ VPN

#### 3.1 Site-to-Site VPN (LAN-to-LAN)
เชื่อมต่อระหว่าง Router/Firewall 2 ฝั่ง โดยที่ **ผู้ใช้ไม่รู้ตัวว่ามี VPN อยู่** เพราะ Tunnel ถูกตั้งค่าไว้ล่วงหน้าที่อุปกรณ์เลย

```
[HQ: 192.168.1.0/24]               [Branch: 10.10.10.0/24]
        |                                     |
     [R1 NAT]========== VPN Tunnel ==========[ R2]
    203.0.113.1                           198.51.100.1
           Public Internet
```
**เหมาะสำหรับ:** เชื่อม Data Center กับสาขา, เชื่อมหลายออฟฟิศ

#### 3.2 Remote Access VPN (Client-to-Site)
พนักงานติดตั้ง **VPN Client Software** บน Notebook/Smartphone แล้วต่อ VPN เอาเองเวลาต้องการใช้

```
[Laptop ที่บ้าน] ----VPN------> [Firewall บริษัท] --> [Server ภายใน]
  พนักงาน WFH    กด Connect แค่ครั้งเดียว
```
**ซอฟต์แวร์ยอดนิยม:** Cisco AnyConnect, FortiClient, OpenVPN, WireGuard, GlobalProtect

#### 3.3 เปรียบเทียบ

| ด้าน | Site-to-Site | Remote Access |
|------|-------------|---------------|
| ผู้ตั้งค่า | Network Admin (ที่ Router) | User ติดตั้ง Client เอง |
| การเชื่อมต่อ | ตลอดเวลา (Always-on) | ต้องกด Connect เอง |
| สำหรับ | เชื่อมสำนักงาน | พนักงาน WFH |
| ความซับซ้อน | สูง (Config Router) | ง่าย (ติดตั้ง App) |

---

### 4. โปรโตคอลหลัก: IPsec

**IPsec (IP Security)** เป็นมาตรฐาน IETF ที่ทำงานใน **Layer 3 (Network Layer)** ประกอบด้วยโปรโตคอลย่อยหลายตัว:

#### 4.1 กระบวนการ IKE (Internet Key Exchange)

**IKE Phase 1 — สร้างอุโมงค์ปลอดภัยสำหรับเจรจา (ISAKMP SA):**
- ตรวจสอบตัวตนกัน (Authentication) ด้วย Pre-Shared Key หรือ Certificate
- ตกลงกัน (Negotiate) เรื่องการเข้ารหัส (Encryption: AES/3DES) และ Hash (SHA/MD5)
- แลก Diffie-Hellman Key กัน เพื่อสร้าง Session Key ร่วมกัน
- ผลลัพธ์: เกิด **ISAKMP SA (Management Tunnel)**

**IKE Phase 2 — สร้าง Tunnel จริงสำหรับ Data (IPsec SA):**
- ตกลงกันอีกครั้งภายใน Management Tunnel ที่สร้างไว้จาก Phase 1
- ระบุว่า Traffic ไหนบ้างที่จะส่งผ่าน VPN (Interesting Traffic / ACL)
- ผลลัพธ์: เกิด **IPsec SA คู่หนึ่ง** (1 SA สำหรับขาออก + 1 SA สำหรับขาเข้า)

```
Phase 1:   Router A <--ISAKMP Negotiate--> Router B
           (พิสูจน์ตัวตน + ตกลงเรื่องรหัส)

Phase 2:   Router A <--IPsec SA Setup--> Router B
           (ตกลง Traffic + เริ่มส่งข้อมูลจริง)
```

#### 4.2 Protocol ที่ใช้ห่อหุ้มข้อมูล

| Protocol | หมายเลข | เข้ารหัสข้อมูล? | ยืนยันตัวตน? | นิยมใช้? |
|----------|---------|----------------|-------------|---------|
| **ESP** (Encapsulating Security Payload) | 50 | ✅ ใช่ | ✅ ใช่ | ✅ ใช้เสมอ |
| **AH** (Authentication Header) | 51 | ❌ ไม่ | ✅ ใช่ | ❌ ไม่นิยม |

> **ESP ใช้มากกว่าเสมอ** เพราะทำได้ทั้ง Encrypt + Authenticate ส่วน AH แค่ Authenticate อย่างเดียวและยังมีปัญหากับ NAT อีกด้วย

#### 4.3 Mode ของ IPsec

| Mode | การห่อหุ้ม | ใช้กับ |
|------|-----------|-------|
| **Tunnel Mode** | ห่อทั้ง Original Header + Data ด้วย New Header | Site-to-Site VPN (ใช้บ่อยที่สุด) |
| **Transport Mode** | ห่อแต่ Data อย่างเดียว Original Header ยังเดิม | Host-to-Host (ใช้น้อยมาก) |

---

### 5. ตัวอย่างคอนฟิก GRE Tunnel (VPN พื้นฐาน)

GRE (Generic Routing Encapsulation) เป็น Tunnel ง่ายๆ ที่ไม่มีการเข้ารหัส แต่ใช้ฝึกหัด Tunneling ได้ดี มักต้องใช้คู่กับ IPsec เพื่อให้มีความปลอดภัย

```shell
! บน Router R1 (HQ: 203.0.113.1 → Branch: 198.51.100.1)
interface Tunnel0
 description GRE Tunnel to Branch
 ip address 172.16.0.1 255.255.255.252
 tunnel source GigabitEthernet0/0     ! Interface ขา WAN ของ R1
 tunnel destination 198.51.100.1       ! IP WAN ปลายทาง R2
 no shutdown

! บน Router R2 (Branch: 198.51.100.1 → HQ: 203.0.113.1)
interface Tunnel0
 description GRE Tunnel to HQ
 ip address 172.16.0.2 255.255.255.252
 tunnel source GigabitEthernet0/0     ! Interface ขา WAN ของ R2
 tunnel destination 203.0.113.1        ! IP WAN ปลายทาง R1
 no shutdown

! เพิ่ม Route ให้วง LAN ปลายทางวิ่งผ่าน Tunnel
ip route 10.10.10.0 255.255.255.0 172.16.0.2
```

---

### 6. เปรียบเทียบโปรโตคอล VPN ยอดนิยม

| โปรโตคอล | ความเร็ว | ความปลอดภัย | ง่ายต่อการตั้งค่า | ข้ามผ่าน NAT/Firewall |
|----------|---------|------------|------------------|-----------------------|
| **IPsec** | ปานกลาง | สูงมาก | ซับซ้อน | บางครั้งติดปัญหา |
| **OpenVPN** | ปานกลาง | สูง | ปานกลาง | ดีมาก (TCP 443) |
| **WireGuard** | เร็วมาก | สูงมาก | ง่าย | ดี (UDP) |
| **L2TP/IPsec** | ช้า | สูง | ง่าย | ปานกลาง |
| **PPTP** | เร็ว | **ต่ำมาก** | ง่ายมาก | ดี (แต่ไม่แนะนำ!) |

---

### 7. คำสั่งตรวจสอบ VPN (Cisco IOS)

```shell
! ดูสถานะ IKE Phase 1 (ISAKMP SA)
show crypto isakmp sa

! ดูสถานะ IKE Phase 2 (IPsec SA)
show crypto ipsec sa

! ดูสถานะ GRE Tunnel Interface
show interfaces Tunnel0

! ดูว่า Tunnel ขึ้นหรือเปล่า
show ip interface brief | include Tunnel

! Debug การ Negotiate IKE (ใช้ตอน Troubleshoot)
debug crypto isakmp
debug crypto ipsec
```

**ผลลัพธ์ตัวอย่าง (VPN ขึ้นสำเร็จ):**
```
Router# show crypto isakmp sa
dst            src            state    conn-id slot status
198.51.100.1   203.0.113.1    QM_IDLE  1001    0    ACTIVE

Router# show crypto ipsec sa
interface: GigabitEthernet0/0
    Crypto map tag: VPN_MAP
    local  addr 203.0.113.1
    remote addr 198.51.100.1
    #pkts encaps: 1523, #pkts encrypt: 1523
    #pkts decaps: 1489, #pkts decrypt: 1489
```

---

## 📊 ตารางสรุปเปรียบเทียบบทที่ 13-17

| หัวข้อ | โปรโตคอล | ทำงานที่ Layer | Port/Protocol | ใช้กับ |
|--------|---------|--------------|--------------|-------|
| ACL | - | Layer 3-4 | - | คัดกรอง Traffic |
| NAT/PAT | - | Layer 3 | - | แปลง Private → Public IP |
| DHCP | UDP | Application | 67 (Server), 68 (Client) | แจก IP อัตโนมัติ |
| DNS | UDP/TCP | Application | 53 | แปลงชื่อ → IP |
| VPN/IPsec | ESP(50)/AH(51)/IKE(UDP500) | Layer 3 | 500, 4500 | Tunnel เข้ารหัส |