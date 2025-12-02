# 🖧 Basic Switch Lab: Connecting PCs to a Switch

> Complete hands-on lab to connect virtual PCs to a Cisco switch, assign IP addresses, and verify Layer 2 connectivity.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [Switch Configuration](#switch-configuration)
6. [PC Configuration](#pc-configuration)
7. [Testing Connectivity](#testing-connectivity)
8. [Verification](#verification)
9. [Troubleshooting](#troubleshooting)
10. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Understand basic switch operation and Layer 2 (data link layer) connectivity in a network.

### By the end of this lab, you will:

- ✅ Create a network lab with switches and PCs in EVE-NG
- ✅ Connect two virtual PCs to a Cisco switch using appropriate interfaces
- ✅ Configure switch ports to support PC connections
- ✅ Assign IP addresses to virtual PCs
- ✅ Test connectivity between PCs using ping command
- ✅ Verify switch port operation and MAC address learning
- ✅ Understand basic Layer 2 switching concepts

---

## ✅ Prerequisites

> **Purpose:** Ensure you have all necessary resources before starting the lab.

### Required Items

| Requirement | Purpose | Details |
|-------------|---------|---------|
| **EVE-NG Lab** | Platform for simulation | Installation completed from previous steps |
| **Cisco Switch Image** | Virtual switch device | IOS or IOS-XE compatible |
| **Virtual PC Image** | PC nodes for testing | VPCS (Virtual PC Simulator) or Alpine Linux |
| **Network Knowledge** | Conceptual understanding | Basic IP addressing and networking |

### Prerequisites Checklist

- ✅ EVE-NG installed and running
- ✅ Access to EVE-NG web interface
- ✅ Cisco switch image available in EVE-NG
- ✅ Virtual PC image available in EVE-NG
- ✅ Basic understanding of IP subnetting

> **ℹ️ Note:** If you need to install images, refer to the EVE-NG documentation on how to add device images.

---

## 📊 Lab Topology

> **Purpose:** Visualize the network structure you'll create.


### Topology Details

| Device | IP Address | Subnet Mask | Gateway | Interface |
|--------|-----------|-------------|---------|-----------|
| **PC1** | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 | eth0 |
| **PC2** | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 | eth0 |
| **SW1** | N/A (Layer 2) | N/A | N/A | Gi0/0, Gi0/1 |

---

## 🔧 Creating the Lab

> **Purpose:** Set up the network topology in EVE-NG.

### Step 1: Create a New Lab

**What:** Create a new lab project in EVE-NG.

**How to:**
1. Log into EVE-NG web interface 
2. Click **Add Lab** in the left menu
3. Enter lab details:
   - **Lab Name**: `Basic_Switch_Lab`
   - **Lab Description**: `Connecting PCs to a Switch`
   - **Lab Version**: `1.0`
4. Click **Create** to save the lab

> **💡 Tip:** Use descriptive names that reflect the lab's purpose for easy identification later.

---

### Step 2: Add Switch Node

**What:** Add a Cisco switch to the lab.

**How to:**
1. In the lab view, click **Add Node**
2. Select **Cisco** → **IOSv** (or C9200 if available)
3. Configure the node:
   - **Node Name**: `SW1`
   - **Device Type**: Select the switch model
   - **Quantity**: `1`
4. Click **Add** to add the switch to the lab

![Add Switch Node](imgs/diagram.png)

> **ℹ️ Note:** IOSv is the most common option for lab environments.

---

### Step 3: Add Virtual PC Nodes

**What:** Add two virtual PCs to the lab.

**How to:**
1. Click **Add Node** again
2. Select **VPCS** (Virtual PC Simulator) or **Hosts** → **Alpine**
3. Configure the nodes:
   - **Node Name**: `PC1`
   - **Device Type**: VPCS
   - **Quantity**: `2` (to create PC1 and PC2 together)
4. Click **Add**

> **💡 Tip:** VPCS is lightweight and ideal for basic connectivity testing.

---

### Step 4: Connect Devices

**What:** Create network connections between devices.

**How to:**
1. Click **Add Link** or drag connections between devices
2. Connect PC1 to SW1 port Gi0/0
3. Connect PC2 to SW1 port Gi0/1
4. Verify connections appear in the topology view

**Connection Summary:**
```
PC1 → SW1 (Gi0/0)
PC2 → SW1 (Gi0/1)
```

> **✅ Checkpoint:** Lab topology is now complete. Save your lab before proceeding.

---

### Step 5: Start the Lab

**What:** Power on all devices to begin.

**How to:**
1. Right-click on the lab name
2. Select **Start Lab** or
3. Click the **Start** button on each device individually
4. Wait for devices to boot (usually 2-3 minutes for the switch)

> **ℹ️ Note:** Virtual switches take longer to boot than PCs due to IOS loading.

---

## ⚙️ Switch Configuration

> **Purpose:** Configure the switch to support PC connections on Layer 2.

### Step 6: Access the Switch Console

**What:** Connect to the switch via console for configuration.

**How to:**
1. Right-click on **SW1** in the topology
2. Select **Console** (or SSH if already configured)
3. A new terminal window will open
4. Press **Enter** to see the prompt

**Expected Output:**
```
Switch>
```

> **Note:** First-time console access shows the basic prompt without a hostname.

---

### Step 7: Enter Privileged Execution Mode

**What:** Access administrative configuration commands.

**Commands:**
```bash
enable
```

**Expected Output:**
```
Switch>enable
Password: (press Enter if no password set)
Switch#
```

> **ℹ️ Note:** The `#` symbol indicates privileged (admin) mode. The `>` indicates user mode.

---

### Step 8: Enter Configuration Mode

**What:** Access global configuration settings.

**Commands:**
```bash
configure terminal
```

**Expected Output:**
```
Switch#configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#
```

> **💡 Tip:** The `(config)#` prompt indicates you're in configuration mode.

---

### Step 9: Configure Switch Hostname

**What:** Give the switch a meaningful name.

**Commands:**
```bash
hostname SW1
```

**Expected Output:**
```
Switch(config)#hostname SW1
SW1(config)#
```

> **Security Tip:** Use descriptive hostnames that indicate location or purpose (e.g., SW-Floor1, SW-Core).

---

### Step 10: Configure Access Ports for PCs

**What:** Activate and configure switch ports for PC connections.

**Commands for PC1 port:**
```bash
interface gigabitEthernet 0/0
description PC1-Connection
no shutdown
exit
```

**Commands for PC2 port:**
```bash
interface gigabitEthernet 0/1
description PC2-Connection
no shutdown
exit
```

**Expected Output:**
```
SW1(config)#interface gigabitEthernet 0/0
SW1(config-if)#description PC1-Connection
SW1(config-if)#no shutdown
SW1(config-if)#exit
SW1(config)#
```

| Command | Purpose |
|---------|---------|
| `interface gigabitEthernet 0/0` | Select the interface |
| `description PC1-Connection` | Add descriptive label |
| `no shutdown` | Activate the port (admin up) |
| `exit` | Return to config mode |

---

### Step 11: Save Switch Configuration

**What:** Save all changes to non-volatile memory.

**Commands:**
```bash
end
copy running-config startup-config
```

**Alternative:**
```bash
write memory
```

**Expected Output:**
```
SW1(config)#end
SW1#write memory
Building configuration...
[OK]
```

> **✅ Checkpoint:** Switch configuration is complete and saved.

---

## 🌐 PC Configuration

> **Purpose:** Configure IP addresses and network settings on the virtual PCs.

### Step 12: Access PC1 Console

**What:** Connect to the first PC.

**How to:**
1. Right-click on **PC1** in the topology
2. Select **Console**
3. A terminal window for PC1 opens

**Expected Output:**
```
PC1>
```

---

### Step 13: Configure PC1 IP Address

**What:** Assign IP address, subnet mask, and gateway to PC1.

**Commands:**
```bash
ip 192.168.1.10 255.255.255.0 192.168.1.1
```

**Expected Output:**
```
PC1>ip 192.168.1.10 255.255.255.0 192.168.1.1
Checking for duplicate address...
PC1 : 192.168.1.10 255.255.255.0 gateway 192.168.1.1
```

| Component | Value | Purpose |
|-----------|-------|---------|
| **IP Address** | 192.168.1.10 | Unique address for PC1 |
| **Subnet Mask** | 255.255.255.0 | Defines network size (/24 = 254 hosts) |
| **Gateway** | 192.168.1.1 | Route to other networks |

---

### Step 14: Verify PC1 Configuration

**What:** Check that the IP address was assigned correctly.

**Commands:**
```bash
show ip
```

**Expected Output:**
```
PC1> show ip
NAME        : PC1
IP/MASK     : 192.168.1.10/24
GATEWAY     : 192.168.1.1
DNS         : 0.0.0.0
DHCP server : 0.0.0.0
DHCP lease  : 0
```

> **💡 Tip:** Verify the IP configuration matches exactly what you assigned.

---

### Step 15: Configure PC2 IP Address

**What:** Assign IP address to the second PC.

**How to:**
1. Right-click on **PC2** and select **Console**
2. Enter the configuration command

**Commands:**
```bash
ip 192.168.1.20 255.255.255.0 192.168.1.1
```

**Expected Output:**
```
PC2>ip 192.168.1.20 255.255.255.0 192.168.1.1
Checking for duplicate address...
PC2 : 192.168.1.20 255.255.255.0 gateway 192.168.1.1
```

> **ℹ️ Note:** PC2 has a different IP address (.20 instead of .10) but the same subnet.

---

### Step 16: Verify PC2 Configuration

**Commands:**
```bash
show ip
```

**Expected Output:**
```
PC2> show ip
NAME        : PC2
IP/MASK     : 192.168.1.20/24
GATEWAY     : 192.168.1.1
DNS         : 0.0.0.0
DHCP server : 0.0.0.0
DHCP lease  : 0
```

---

## 🔍 Testing Connectivity

> **Purpose:** Verify that the network connection is working correctly between PCs.

### Step 17: Test PC1 to PC2 Connectivity

**What:** Use ping to verify connectivity from PC1 to PC2.

**How to:**
1. In PC1 console, execute:

```bash
ping 192.168.1.20
```

**Expected Output:**
```
PC1>ping 192.168.1.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echoes to 192.168.1.20, timeout is 2 seconds:
!!!!.
Success rate is 80 percent (4/5), roundtrip min/avg/max = 1/2/5 ms
```

![Result Image](imgs/result.png)

| Symbol | Meaning |
|--------|---------|
| **!** | Reply received (success) |
| **.** | No reply (timeout) |

> **✅ Success:** If you see `!` characters, connectivity is working!

---

### Step 18: Test PC2 to PC1 Connectivity

**What:** Verify bidirectional connectivity by pinging from PC2.

**Commands:**
```bash
ping 192.168.1.10
```

**Expected Output:**
```
PC2>ping 192.168.1.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echoes to 192.168.1.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), roundtrip min/avg/max = 1/1/2 ms
```

> **✅ Checkpoint:** Bidirectional connectivity verified. Both PCs can communicate!

---

## ✔️ Verification

> **Purpose:** Confirm the lab is functioning correctly.

### Step 19: Verify Switch Port Status

**What:** Check that switch ports are active and passing traffic.

**Commands (from SW1 console):**
```bash
show interfaces status
```

**Expected Output:**
```
SW1#show interfaces status

Interface   Status        Protocol Description
Gi0/0       connected     up       PC1-Connection
Gi0/1       connected     up       PC2-Connection
Gi0/2       notconnect    down
...
```

| Status | Meaning |
|--------|---------|
| **connected** | Cable is connected |
| **up** | Port is active and working |
| **notconnect** | No device connected |
| **down** | Port is inactive |

---

### Step 20: View MAC Address Table

**What:** See which MAC addresses the switch has learned on each port.

**Commands:**
```bash
show mac address-table
```

**Expected Output:**
```
SW1#show mac address-table
Mac Address Table
-------------------------------------------
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.79c8.1234    DYNAMIC     Gi0/0
   1    0050.79c8.5678    DYNAMIC     Gi0/1
----    -----------       --------    -----
```

> **ℹ️ Note:** The switch learns MAC addresses automatically when devices communicate.

---

### Step 21: Display Running Configuration

**What:** Review the active configuration on the switch.

**Commands:**
```bash
show running-config
```

**Key Sections to Look For:**
```
hostname SW1
!
interface GigabitEthernet0/0
 description PC1-Connection
 no shutdown
!
interface GigabitEthernet0/1
 description PC2-Connection
 no shutdown
```

---

## 🆘 Troubleshooting

| Issue | Symptoms | Cause | Solution |
|-------|----------|-------|----------|
| **Ping fails** | `.....` in ping output | Port down, wrong IP subnet | Check `show int status`; verify IP with `show ip` on PC |
| **Switch port not active** | `notconnect` status | Port disabled with `shutdown` | Run `no shutdown` on the port |
| **PC can't get IP** | `0.0.0.0` when checking IP | IP configuration error | Re-run `ip <address> <mask> <gateway>` |
| **One-way connectivity** | Ping works one direction only | Routing or ARP issue | Test from both directions; check switch MAC table |
| **Devices not visible** | Can't see PC or switch in lab | Device not started | Right-click device, select **Start** |
| **Slow response** | Ping times very high (>100ms) | VM resource constraints | Reduce other running VMs; increase EVE-NG RAM |

### Common Commands for Troubleshooting

**On PCs:**
```bash
show ip                    # Display current IP configuration
ping <destination>         # Test connectivity
show arp                   # View ARP cache (learned IP-to-MAC mappings)
```

**On Switch:**
```bash
show interfaces brief      # Quick overview of all interfaces
show interfaces Gi0/0      # Detailed info on specific port
show running-config        # Current active configuration
```

---

## ✅ Summary & Next Steps

### Lab Completion

**Congratulations!** You have successfully completed the Basic Switch Lab:

- ✅ Created network lab topology with switch and PCs
- ✅ Configured switch ports to support PC connections
- ✅ Assigned IP addresses to both virtual PCs
- ✅ Tested bidirectional connectivity using ping
- ✅ Verified switch port operation and MAC address learning
- ✅ Confirmed data link layer (Layer 2) connectivity

### Key Concepts Learned

1. **Layer 2 Switching**: How switches forward frames based on MAC addresses
2. **Switch Ports**: Access ports for end devices vs trunk ports for switches
3. **IP Addressing**: Assigning addresses within the same subnet
4. **ICMP & Ping**: Testing Layer 3 connectivity over Layer 2 infrastructure
5. **MAC Learning**: How switches dynamically learn port-to-MAC associations

### What's Next?

**Choose your next topic based on your learning goals:**

1. **VLAN Segmentation:**
   - Create multiple VLANs on the switch
   - Assign ports to different VLANs
   - Test isolation and inter-VLAN routing

2. **Switch Security:**
   - Configure port security to limit MAC addresses
   - Set up AAA authentication
   - Enable SSH for secure management

3. **Redundancy & Load Balancing:**
   - Add additional switches to the topology
   - Configure Spanning Tree Protocol (STP)
   - Create EtherChannel link aggregation

4. **Advanced Switching:**
   - Configure Dynamic VLAN assignment
   - Set up VLAN Trunking Protocol (VTP)
   - Implement Quality of Service (QoS)

---

## 📚 Useful Resources

- [Cisco Switch Fundamentals](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst9200/software/release/16_11/configuration_guide/b_c9200_cg_16_11/b_c9200_cg_16_11_chapter_011.pdf)
- [VPCS Documentation](https://github.com/GNS3/cisco-ios-l2-15.2-bin)
- [IP Subnetting Guide](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html)
- [EVE-NG Lab Documentation](https://www.eve-ng.net/index.php/documentation/)
- [Ping Command Reference](https://www.cisco.com/c/en/us/td/docs/ios/fundamentals/command/reference/cf_book.pdf)

---

✅ **Basic switch lab completed successfully!** 🎓

**You now understand Layer 2 connectivity! Ready for advanced switching? 🚀**
