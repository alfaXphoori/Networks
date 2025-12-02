# 🖧 VLAN Configuration Lab

> Configure VLANs on switches, set up trunk links, and verify VLAN segmentation.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Lab Setup](#lab-setup)
3. [VLAN Configuration](#vlan-configuration)
4. [Trunk Configuration](#trunk-configuration)
5. [Testing Connectivity](#testing-connectivity)
6. [Summary & Next Steps](#summary--next-steps)
7. [Troubleshooting](#troubleshooting)

---

## 🎯 Lab Objectives

> **Purpose:** Learn to segment networks using VLANs and establish trunk links.

- ✅ Create VLANs on switches
- ✅ Assign ports to VLANs
- ✅ Configure trunk links between switches
- ✅ Verify VLAN configuration and connectivity

---

## 🛠️ Lab Setup

> **Purpose:** Create a multi-switch VLAN topology.

### Step 1: Create Lab Topology

**What:** Set up devices and connections for VLAN testing.

**How to:**
1️⃣ Create a new lab named "VLAN_Lab"
2️⃣ Add devices:
   - 🖧 Two Cisco Switches (SW1, SW2)
   - 🖥️ Four Virtual PCs (PC1, PC2, PC3, PC4)
3️⃣ Connect devices:
   - 🔌 PC1 → SW1 (Gi0/0)
   - 🔌 PC2 → SW1 (Gi0/1)
   - 🔌 PC3 → SW2 (Gi0/0)
   - 🔌 PC4 → SW2 (Gi0/1)
   - 🔌 SW1 ↔ SW2 (Gi1/3 - Trunk)

![diagram](imgs/diagram.png)

> **✅ Checkpoint:** Topology created successfully.

---

## ⚙️ VLAN Configuration

> **Purpose:** Create VLANs and assign ports to them.

### Step 2: Configure VLANs on SW1 and SW2

#### 🖧 SW1 Configuration

**Create VLANs:**

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

**Assign Ports to VLANs:**

```bash
interface gigabitEthernet 0/0
switchport mode access
switchport access vlan 10
exit

interface gigabitEthernet 0/1
switchport mode access
switchport access vlan 20
exit
```

#### 🖧 SW2 Configuration

**Create VLANs:**

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

**Assign Ports to VLANs:**

```bash
interface gigabitEthernet 0/0
switchport mode access
switchport access vlan 10
exit

interface gigabitEthernet 0/1
switchport mode access
switchport access vlan 20
exit
```

> **ℹ️ Note:** Both switches must have the same VLAN IDs to communicate across trunk links.

---

## 🔄 Trunk Configuration

> **Purpose:** Configure trunk links between switches to carry multiple VLANs.

### Step 3: Configure Trunk Between SW1 and SW2

**🖧 On SW1:**

```bash
interface gigabitEthernet 1/3
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20
exit
```

**🖧 On SW2:**

```bash
interface gigabitEthernet 1/3
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20
exit
```

> **✅ Checkpoint:** Trunk link configured. VLAN 10 & 20 can now communicate across switches.

---

## 🔍 Testing Connectivity

> **Purpose:** Verify VLAN configuration and test isolation between VLANs.

### Step 4: Verify VLAN Configuration

**📋 Check VLANs on Both Switches:**

```bash
show vlan brief
```

![vlan](imgs/vlanBr.png)

> **Expected Output:** VLAN 10 (Sales) and VLAN 20 (IT) listed with assigned ports.

**📋 Check Trunk Ports:**

```bash
show interfaces trunk
```

![trunk](imgs/intTrunk.png)

> **Expected Output:** Gi1/3 showing as trunk with VLAN 10,20 allowed.

---

### Step 5: Configure PCs and Test Connectivity

**🖥️ PC IP Configuration:**

| Device | VLAN | IP Address | Subnet Mask | Gateway |
|--------|------|-----------|-------------|---------|
| PC1 | 10 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC3 | 10 | 192.168.10.30 | 255.255.255.0 | 192.168.10.1 |
| PC2 | 20 | 192.168.20.20 | 255.255.255.0 | 192.168.20.1 |
| PC4 | 20 | 192.168.20.40 | 255.255.255.0 | 192.168.20.1 |

**Configure IPs:**
```bash
ip 192.168.10.10 255.255.255.0 192.168.10.1  # PC1
ip 192.168.10.30 255.255.255.0 192.168.10.1  # PC3
ip 192.168.20.20 255.255.255.0 192.168.20.1  # PC2
ip 192.168.20.40 255.255.255.0 192.168.20.1  # PC4
```

**📡 Test Connectivity Results:**

| Test | Expected | Result |
|------|----------|--------|
| PC1 ↔ PC3 (VLAN 10) | Success ✅ | Same VLAN |
| PC2 ↔ PC4 (VLAN 20) | Success ✅ | Same VLAN |
| PC1 ↔ PC2 (Different VLAN) | Fail ❌ | Blocked by switch |
| PC3 ↔ PC4 (Different VLAN) | Fail ❌ | Blocked by switch |

**Verify from PC1:**

```bash
ping 192.168.10.30   # Should succeed ✅
ping 192.168.20.20   # Should fail ❌
```

![ping](imgs/ping.png)

> **✅ Success:** VLAN isolation working perfectly!

---

## ✅ Summary & Next Steps

**🎓 Accomplished:**
- ✅ Created VLANs (10-Sales, 20-IT) on multiple switches
- ✅ Assigned ports to VLANs in access mode
- ✅ Configured trunk links (802.1Q) between switches
- ✅ Verified VLAN isolation and connectivity
- ✅ Tested intra-VLAN and inter-VLAN communication

**📊 VLAN Topology Summary:**

```
┌─────────────────┐          Trunk (Gi1/3)          ┌─────────────────┐
│      SW1        │◄─────────────────────────────►│      SW2        │
│   (Sales/IT)    │                                   │   (Sales/IT)    │
└────┬──────┬─────┘                                   └────┬──────┬─────┘
     │      │                                             │      │
   Gi0/0  Gi0/1                                         Gi0/0  Gi0/1
     │      │                                             │      │
   PC1(V10) PC2(V20)                                  PC3(V10) PC4(V20)
```

**🚀 Next Steps:**

1. **Inter-VLAN Routing:**
   - Configure router-on-a-stick (RoaS)
   - Enable communication between VLANs
   - Learn about subinterfaces and 802.1Q tagging

2. **Advanced VLAN Features:**
   - Voice VLAN (VoIP) configuration
   - Protected ports / private VLANs
   - VLAN access control lists (VACLs)
   - Dynamic VLAN assignment (VLAN Trunking Protocol - VTP)

3. **Network Segmentation:**
   - Create department-specific VLANs
   - Implement VLAN security policies
   - Configure VLAN pruning on trunks
   - Set up native VLAN security

---

## 🆘 Troubleshooting

| 🔴 Issue | 🔧 Solution |
|---------|-----------|
| **Trunk not carrying VLANs** | Verify `switchport mode trunk` and `switchport trunk allowed vlan` settings |
| **VLAN doesn't appear in list** | Confirm VLAN created with `vlan` command; verify with `show vlan id <number>` |
| **Trunk ports show as down** | Use `no shutdown` on trunk interfaces; verify physical cable connections |
| **Cross-VLAN ping succeeds** | This is abnormal - check if VLAN assignment is correct; verify port modes |
| **Can't ping same VLAN members** | Check IP addresses and subnet masks; ensure they're in same network range |
| **Port won't enter access VLAN** | Remove any existing configurations; use `no switchport mode dynamic` first |

---

## 📚 Useful Commands

```bash
# ===== VLAN Commands =====
show vlan                      # Display all VLANs
show vlan brief               # Show VLAN summary
show vlan id 10               # Show specific VLAN
show vlan name Sales          # Show VLAN by name
create vlan 10                # Create new VLAN
no vlan 10                    # Delete VLAN

# ===== Port Configuration =====
show interfaces               # Show all interfaces
show interface Gi0/0          # Show specific interface
show interface Gi0/0 switchport  # Show port VLAN status
show running-config interface Gi0/0  # Show port config

# ===== Trunk Commands =====
show interfaces trunk         # Show trunk ports
show interfaces trunk allowed-vlan  # Show allowed VLANs on trunks
show spanning-tree vlan 10    # Show STP for VLAN

# ===== Save Configuration =====
write memory                  # Save running config
copy running-config startup-config  # Alternative save method
```

---

## 💡 Best Practices

✅ **Always use meaningful VLAN names** (Sales, IT, Guest, Voice)
✅ **Document your VLAN scheme** (Keep list of VLANs and purposes)
✅ **Use consistent IP addressing** (10.x.x.x for VLAN 10, 20.x.x.x for VLAN 20)
✅ **Test connectivity** after any VLAN changes
✅ **Implement VLAN security** (Block unused ports, set native VLAN carefully)
✅ **Monitor VLAN traffic** for unusual patterns
✅ **Plan for growth** (Leave room for new VLANs)

---

✅ **VLAN configuration lab completed successfully!** 🚀

**Ready to implement Inter-VLAN Routing! 🎓**