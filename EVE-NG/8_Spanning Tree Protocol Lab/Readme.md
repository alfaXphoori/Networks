# 🚀 Spanning Tree Protocol (STP) Lab in EVE-NG

## 🔹 Lab Objectives

- **Understand loop prevention in Layer 2 networks**  
- **Configure Rapid PVST+ on Cisco switches**  
- **Change root bridge priority to control STP behavior**

---

## 🛠 Step 1: Set Up the Lab in EVE-NG

- 1️⃣ Open **EVE-NG**.
- 2️⃣ Create a **New Lab** (e.g., "STP_Lab").
- 3️⃣ **Add Devices**:
  - **Three Cisco Switches (SW1, SW2, SW3)**
  - **Two Virtual PCs (PC1 & PC2)**
- 4️⃣ **Connect Devices**:
  - 🔌 **PC1 → SW1 (GigabitEthernet 0/1)**
  - 🔌 **PC2 → SW3 (GigabitEthernet 0/1)**
  - 🔌 **SW1 → SW2 (GigabitEthernet 1/1)**
  - 🔌 **SW2 → SW3 (GigabitEthernet 1/2)**
  - 🔌 **SW3 → SW1 (GigabitEthernet 1/3) [Loop Connection]**
- 5️⃣ **Diagram**:
  - ![diagram](imgs/diagram.png)

---

## 🔹 Step 2: Enable Spanning Tree on Switches

### 🔹 **On All Switches (SW1, SW2, SW3)**

```bash
enable
configure terminal
spanning-tree mode rapid-pvst
exit
```

✅ **This enables Rapid PVST+ on all switches.**

---

## 🔹 Step 3: Change Root Bridge Priority

### 🔹 **Set SW1 as the Root Bridge**

```bash
interface vlan 1
spanning-tree vlan 1 priority 4096
exit
```

### 🔹 **Set SW2 as the Secondary Root Bridge**

```bash
interface vlan 1
spanning-tree vlan 1 priority 8192
exit
```

✅ **SW1 is now the root bridge, and SW2 is the secondary.**

---

## 🔹 Step 4: Verify Spanning Tree Configuration

### 📋 **Check STP Status on Each Switch**

```bash
show spanning-tree
```

✅ **This will display root bridge information and port roles.**

---

## 🔹 Step 5: Test Loop Prevention

### 🔹 **Check Blocked Ports on SW3**

```bash
show spanning-tree blockedports
```

✅ **At least one port should be in a blocking state to prevent loops.**

### 🔹 **Disconnect an Active Link and Observe STP Convergence**

```bash
show spanning-tree
```

✅ **STP should automatically adjust the network topology.**

---
