# 🖧 Basic Switch Lab: Connecting PCs to a Switch

> Connect two virtual PCs to a Cisco switch, assign IP addresses, and test connectivity.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Lab Setup](#lab-setup)
3. [Switch Configuration](#switch-configuration)
4. [PC Configuration](#pc-configuration)
5. [Testing Connectivity](#testing-connectivity)
6. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Understand basic switch operation and Layer 2 connectivity.

- ✅ Connect two PCs to a Cisco switch
- ✅ Assign IP addresses to both PCs
- ✅ Test connectivity using ping
- ✅ Verify switch port operation

---

## 🛠️ Lab Setup

> **Purpose:** Create the network topology in EVE-NG.

### Step 1: Create Lab and Add Devices

**What:** Set up the basic lab structure with necessary devices.

**How to:**
1. Open EVE-NG web interface
2. Create a new lab named "Basic_Switch_Lab"
3. Add devices:
   - One Cisco Switch (SW1)
   - Two Virtual PCs (PC1 & PC2)
4. Connect devices:
   - PC1 → SW1 (GigabitEthernet 0/0)
   - PC2 → SW1 (GigabitEthernet 0/1)

![diagram](imgs/diagram.png)

> **✅ Checkpoint:** Lab topology created successfully.

---

## ⚙️ Switch Configuration

> **Purpose:** Configure the switch to support PC connections.

### Step 2: Access SW1 and Set a Hostname

**What:** Connect to the switch and give it a meaningful name.

**How to:**

```bash
enable
configure terminal
hostname SW1
```

---

### Step 3: Enable Ports for PC1 and PC2

**What:** Activate the switch ports that connect to the PCs.

**How to:**

```bash
interface gigabitEthernet 0/0
no shutdown
exit

interface gigabitEthernet 0/1
no shutdown
exit
```

> **✅ Checkpoint:** Ports are now active for the PCs.

---

## 🌐 PC Configuration

> **Purpose:** Configure IP addresses on both virtual PCs.

### Step 4: Assign IP Addresses to PCs

**What:** Configure PC network settings.

**How to:**

**On PC1:**

```bash
ip 192.168.1.10 255.255.255.0 192.168.1.1
```

**On PC2:**

```bash
ip 192.168.1.20 255.255.255.0 192.168.1.1
```

> **ℹ️ Note:** Both PCs are now in the same network (192.168.1.0/24).

---

## 🔍 Testing Connectivity

> **Purpose:** Verify that the network connection is working correctly.

### Step 5: Test Connectivity

**What:** Use ping to verify connectivity between PCs.

**How to:**

From **PC1**, ping PC2:

```bash
ping 192.168.1.20
```

![result](imgs/result.png)

> **✅ Success:** If ping replies are received, the network is working!

---

## ✅ Summary & Next Steps

**Congratulations!** You have:
- ✅ Created a basic switch lab topology
- ✅ Configured switch hostname and ports
- ✅ Assigned IP addresses to both PCs
- ✅ Tested bidirectional connectivity

### What's Next?

1. **VLAN Configuration:**
   - Create multiple VLANs
   - Assign PCs to different VLANs
   - Test inter-VLAN communication

2. **Switch Security:**
   - Configure port security
   - Set up SSH management
   - Enable AAA authentication

3. **Redundancy:**
   - Add more switches
   - Configure Spanning Tree Protocol
   - Create EtherChannel aggregation

---

## 🆘 Troubleshooting

| Issue | Solution |
|-------|----------|\n| **Ping fails** | Check IP addresses match same subnet; verify switch ports are active |
| **Switch port not active** | Confirm `no shutdown` command executed; check cable connection |
| **PC can't get IP** | Restart PC; check DHCP or manually assign IP |
| **One-way connectivity** | Verify both PCs have correct subnet mask and gateway |

---

## 📚 Useful Commands

```bash
# Show switch configuration
show running-config

# Show VLAN information
show vlan

# Show interface status
show interfaces

# Show specific interface
show interface gigabitEthernet 0/0

# PC commands
show ip
ping <destination>
save
```

---

✅ **Basic switch lab completed successfully!** 🚀

**Ready to explore advanced switching concepts! 🎓**
