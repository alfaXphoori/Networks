# 🚀 Inter-VLAN Routing Lab in EVE-NG

## 🔹 Lab Objectives
1️⃣ **Create VLANs on the switch**  
2️⃣ **Configure trunking between the switch and router**  
3️⃣ **Set up subinterfaces on the router for Inter-VLAN Routing**  
4️⃣ **Assign IP addresses to PCs**  
5️⃣ **Test connectivity using ping**

---

## 🛠 Step 1: Set Up the Lab in EVE-NG

1. Open **EVE-NG**.
2. Create a **New Lab** (e.g., "Inter-VLAN_Lab").
3. **Add Devices**:
   - **One Cisco Router (R1)**
   - **One Cisco Switch (SW1)**
   - **Two Virtual PCs (PC1 & PC2)**
4. **Connect Devices**:
   - **PC1 → SW1 (Fa0/1)**
   - **PC2 → SW1 (Fa0/2)**
   - **SW1 → R1 (Fa0/24) as a trunk link**

---

## 🔹 Step 2: Configure VLANs on SW1

### 1️⃣ Create VLANs
```bash
enable
configure terminal
vlan 10
name Sales
exit
vlan 20
name IT
exit
```
### 2️⃣ Assign VLANs to Ports
```bash
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit

interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
exit
```
### 3️⃣ Configure a Trunk Link to R1
```bash
interface fastEthernet 0/24
switchport mode trunk
switchport trunk allowed vlan 10,20
exit
```
✅ **Now, the router can receive VLAN-tagged traffic from SW1.**

---

## 🔹 Step 3: Configure Router-on-a-Stick (RoaS) on R1

### 1️⃣ Configure Subinterfaces
```bash
enable
configure terminal
interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit

interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit

no shutdown
exit
```
✅ **Now, the router is ready to route between VLANs.**

---

## 🔹 Step 4: Assign IP Addresses to PCs

### 🖥 **On PC1 (VLAN 10)**
```bash
ip 192.168.10.10 255.255.255.0 192.168.10.1
```
### 🖥 **On PC2 (VLAN 20)**
```bash
ip 192.168.20.10 255.255.255.0 192.168.20.1
```
✅ **Each PC is assigned an IP within its VLAN subnet.**

---

## 🔹 Step 5: Test Inter-VLAN Connectivity

1️⃣ **From PC1, ping PC2**:
```bash
ping 192.168.20.10
```
2️⃣ **From PC2, ping PC1**:
```bash
ping 192.168.10.10
```
✅ **If successful, VLAN communication is working!**

---