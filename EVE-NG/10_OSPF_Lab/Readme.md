# 🚀 OSPF Lab in EVE-NG

## 🎯 **Lab Objectives**
- 🔧 Configure **OSPF v2** on all routers.
- 🔄 Verify **OSPF adjacency and routing tables**.
- 📶 Test **end-to-end connectivity** between PCs.

---

## 🛠 **Step 1: Set Up the Lab in EVE-NG**

### 📌 **Devices Used:**
- 📟 **Three Cisco Routers (R1, R2, R3)**
- 🖥 **Three Virtual PCs (PC1, PC2, PC3)**

### 🔌 **Network Topology & Connections:**
- **PC1** 🖥 ↔ **R1 (Gi0/0) 📟 **192.168.10.10 ↔ 192.168.10.1/24**
- **PC2** 🖥 ↔ **R2 (Gi0/0) 📟 **192.168.20.20 ↔ 192.168.20.1/24**
- **PC3** 🖥 ↔ **R3 (Gi0/0) 📟 **192.168.30.30 ↔ 192.168.30.1/24**
- **R1 (Gi0/1) ↔ R2 (Gi0/1)** 📟 **10.1.1.1/24 ↔ 10.1.1.2/24**
- **R2 (Gi0/2) ↔ R3 (Gi0/2)** 📟 **10.1.10.1/24 ↔ 10.1.10.2/24**

📌 **Diagram:**
![OSPF Diagram](imgs/diagram.png)

---

## 🔹 **Step 2: Configure IP Addresses and OSPF on Routers**

### 📟 **On R1**

#### Configure IP Addresses

```bash
enable
configure terminal
interface gigabitEthernet 0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
exit

interface gigabitEthernet 0/1
ip address 10.1.1.1 255.255.255.0
no shutdown
exit
```

#### Configure EIGRP

```bash
router ospf 1
network 192.168.10.0 0.0.0.255 area 0
network 10.1.1.0 0.0.0.255 area 0
exit
```

### 📟 **On R2**

#### Configure IP Addresses

```bash
enable
configure terminal
interface gigabitEthernet 0/0
ip address 192.168.20.1 255.255.255.0
no shutdown
exit

interface gigabitEthernet 0/1
ip address 10.1.1.2 255.255.255.0
no shutdown
exit

interface gigabitEthernet 0/2
ip address 10.1.10.1 255.255.255.0
no shutdown
exit
```

#### Configure EIGRP

```bash
router ospf 1
network 192.168.20.0 0.0.0.255 area 0
network 10.1.1.0 0.0.0.255 area 0
network 10.1.10.0 0.0.0.255 area 0
exit
```

### 📟 **On R3**

#### Configure IP Addresses

```bash
enable
configure terminal
interface gigabitEthernet 0/0
ip address 192.168.30.1 255.255.255.0
no shutdown
exit

interface gigabitEthernet 0/2
ip address 10.1.10.2 255.255.255.0
no shutdown
exit
```

#### Configure EIGRP

```bash
router ospf 1
network 192.168.30.0 0.0.0.255 area 0
network 10.1.10.0 0.0.0.255 area 0
exit
```

✅ **IP addressing and OSPF is now configured on all routers.**

---

## 🔹 **Step 3: Verify OSPF Configuration**

### 📜 **Check OSPF Neighbors**
```bash
show ip ospf neighbor
```
📌 **This command will show OSPF adjacency status.**

### 📜 **Check OSPF Routes**
```bash
show ip route ospf
```
📌 **Displays learned OSPF routes.**
![route](imgs/route.png)

✅ **If neighbors are established and routes are present, OSPF is working correctly.**

---

## 🔹 **Step 5: Configure PCs and Test Connectivity**

### 🖥 **On PC1**
```bash
ip 192.168.10.10 255.255.255.0 192.168.10.1
```

### 🖥 **On PC2**
```bash
ip 192.168.20.20 255.255.255.0 192.168.20.1
```

### 🖥 **On PC3**
```bash
ip 192.168.30.30 255.255.255.0 192.168.30.1
```

✅ **All PCs are now connected to their respective routers.**

### 🔄 **Test Connectivity**

#### 📡 **From PC1, ping PC2 , ping PC3**
```bash
ping 192.168.20.20
ping 192.168.30.30
```
![ping](imgs/ping.png)

✅ **If pings are successful, OSPF is routing traffic correctly!**

---
