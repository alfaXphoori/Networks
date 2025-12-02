# 🖧 Switch Basic Configuration

> Complete guide to configure Cisco switches with hostnames, passwords, SSH access, and management IP addresses for secure network management.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Prerequisites & Requirements](#prerequisites--requirements)
2. [Adding Switch Nodes](#adding-switch-nodes)
3. [Console Connection Setup](#console-connection-setup)
4. [Basic Switch Configuration](#basic-switch-configuration)
5. [Management IP & SSH Configuration](#management-ip--ssh-configuration)
6. [Troubleshooting](#troubleshooting)
7. [Summary & Next Steps](#summary--next-steps)

---

## ✅ Prerequisites & Requirements

> **Purpose:** Ensure you have the necessary tools and access to configure Cisco switches.

### What You'll Need

| Item | Purpose | Details |
|------|---------|---------|
| **Cisco Switch (Virtual or Physical)** | Device to configure | Cisco 2950, 3560, 9200, or similar |
| **EVE-NG Lab** | Virtualization platform | Already installed from previous steps |
| **PuTTY or SSH Client** | Console/remote access | Windows, Linux, or macOS |
| **Console Cable** | Physical connection | USB-to-Serial adapter or direct serial port |

### Prerequisites

- ✅ EVE-NG installed and running
- ✅ Network lab with switch nodes created
- ✅ Basic understanding of Cisco CLI
- ✅ SSH client installed on host machine

> **Note:** This guide uses virtual switches in EVE-NG. For physical switches, use console cables instead.

---

## 🔧 Adding Switch Nodes

> **Purpose:** Create and add switch nodes to your EVE-NG network lab.

### Step 1: Add Switch Nodes to Lab

**What:** Add virtual Cisco switches to your lab topology.

**How to:**
1. In EVE-NG web interface, open your lab
2. Click on the **Add Node** button
3. Select **Cisco IOS** or **Cisco Catalyst** switches
4. Configure the node:
   - **Node Name**: `SW1` (or desired name)
   - **Device Type**: Choose switch model (e.g., IOSv or C9200)
5. Click **Add**

![Add Switch Node](imgs/addSw.png)

> **💡 Tip:** Add multiple switches if you need a multi-switch topology for VLAN testing.

---

### Step 2: Connect Switch to Network

**What:** Connect the switch to the network topology.

**How to:**
1. Drag cables from the switch to connect to other devices
2. Connect to routers, PCs, or other switches as needed
3. Right-click the switch and select **Start** to power it on
4. Wait for the switch to boot (2-3 minutes)

> **ℹ️ Note:** Virtual switch boot time depends on your host computer's resources.

---

## 🌐 Console Connection Setup

> **Purpose:** Connect to the switch console for initial configuration.

### Step 3: Launch Console Client

**What:** Open a console connection to the switch.

**How to:**
1. In EVE-NG, right-click the switch node
2. Select **Console** or **SSH** (depending on current switch state)
3. A new terminal window will open

![PuTTY Console Connection](imgs/puttySW1.png)

> **💡 Tip:** The first connection typically requires console access. After SSH is configured, you can use SSH instead.

---

### Step 4: Initial Configuration Prompt

**What:** The switch prompts for initial setup.

**Options:**
- `yes` - Start setup dialog (simplified configuration)
- `no` - Skip to command line (recommended for this guide)

**Action:** Enter `no` to skip the setup dialog and access the CLI directly.

> **ℹ️ Note:** Using the CLI directly gives more control over configuration options.

---

## 🖧 Basic Switch Configuration

> **Purpose:** Configure fundamental switch settings including hostname, passwords, and security settings.

### Step 5: Enter Privileged Execution Mode

**What:** Access administrative configuration commands.

**Command:**
```bash
enable
```

**Expected Output:**
```
Switch>enable
Password: (press Enter if no password is set)
Switch#
```

> **Note:** The `#` prompt indicates privileged mode.

---

### Step 6: Enter Configuration Terminal

**What:** Access the global configuration mode.

**Command:**
```bash
configure terminal
```

**Expected Output:**
```
Switch#configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#
```

---

### Step 7: Set Device Hostname

**What:** Give your switch a meaningful name.

**Command:**
```bash
hostname SW1
```

**Expected Output:**
```
Switch(config)#hostname SW1
SW1(config)#
```

> **💡 Tip:** Use descriptive names like `SW-Floor1`, `SW-Core`, or `SW-Access` to identify the switch's location or role.

---

### Step 8: Set Console Access Password

**What:** Require a password for console connections.

**Commands:**
```bash
line console 0
password cisco
login
exit
```

**Step-by-Step:**
1. `line console 0` - Access console port settings
2. `password cisco` - Set password (change `cisco` to your choice)
3. `login` - Enable login prompt
4. `exit` - Return to configuration mode

> **⚠️ Warning:** Save your password securely. You'll need it for future console access.

---

### Step 9: Set Privileged EXEC Password

**What:** Require a password for the `enable` command.

**Command:**
```bash
enable secret cisco1234
```

> **Security Tip:** The `secret` keyword encrypts the password. Use `enable password` for unencrypted (not recommended).

---

### Step 10: Set MOTD Banner

**What:** Display a welcome message when users connect.

**Command:**
```bash
banner motd $
--------------------------------------------
Switch 1 : Cisco 9200 : Floor 1 : CE Tower
--------------------------------------------
$
```

**Breakdown:**
- `banner motd` - Message of the Day
- `$` - Delimiter (use any character not in your message)
- Type your message between the delimiters
- End with the same delimiter on a new line

> **ℹ️ Note:** The banner displays before the login prompt.

---

### Step 11: Disable DNS Lookups (Optional but Recommended)

**What:** Speed up command entry by preventing DNS lookup delays.

**Command:**
```bash
no ip domain-lookup
```

> **💡 Tip:** When you mistype a command, the switch won't try to resolve it as a hostname.

---

## 🌐 Management IP & SSH Configuration

> **Purpose:** Configure remote access to the switch via SSH for secure management.

### Step 12: Assign Management IP Address

**What:** Give the switch an IP address for remote management.

**Commands:**
```bash
interface vlan 1
ip address 192.168.1.100 255.255.255.0
no shutdown
exit
```

**Details:**
- `interface vlan 1` - Access VLAN 1 (default management VLAN)
- `ip address 192.168.1.100 255.255.255.0` - Set IP and netmask
- `no shutdown` - Enable the interface
- `exit` - Return to configuration mode

> **Configuration Table:**

| Setting | Example | Purpose |
|---------|---------|---------|
| **IP Address** | 192.168.1.100 | Unique address for switch |
| **Subnet Mask** | 255.255.255.0 | Network boundary (/24 = 254 hosts) |
| **VLAN** | 1 | Default management VLAN |

---

### Step 13: Set Default Gateway

**What:** Configure the route for off-network traffic.

**Command:**
```bash
ip default-gateway 192.168.1.1
```

> **ℹ️ Note:** The gateway IP should be the router's IP address on this network segment.

---

### Step 14: Enable SSH - Part 1: Configure Domain

**What:** Create the domain name required for SSH key generation.

**Command:**
```bash
ip domain-name lab.local
```

> **Alternative:** Use `ip domain-name example.com` for any valid domain name (doesn't need to be real for lab environments).

---

### Step 15: Enable SSH - Part 2: Generate RSA Keys

**What:** Create cryptographic keys for secure SSH connections.

**Command:**
```bash
crypto key generate rsa
```

**When Prompted:**
```
How many bits in the modulus [512]: 2048
```

Enter `2048` for stronger encryption (recommended).

> **Security Note:** Higher key size = stronger security but slightly slower performance. 2048-bit is industry standard.

---

### Step 16: Create SSH User Account

**What:** Create a local user for SSH login.

**Command:**
```bash
username admin privilege 15 secret admin1234
```

**Breakdown:**
- `username admin` - Username for SSH login
- `privilege 15` - Highest privilege level (full administrative access)
- `secret admin1234` - Password (encrypted)

> **⚠️ Security Tip:** Use strong passwords with uppercase, lowercase, numbers, and special characters.

---

### Step 17: Configure VTY Lines for SSH

**What:** Configure virtual terminal lines to accept SSH connections only.

**Commands:**
```bash
line vty 0 4
transport input ssh
login local
exit
```

**Details:**
- `line vty 0 4` - Configure first 5 virtual terminal lines
- `transport input ssh` - Accept only SSH (not Telnet)
- `login local` - Use local username database for authentication
- `exit` - Return to configuration mode

> **Security Improvement:** Using SSH instead of Telnet encrypts all traffic and credentials.

---

### Step 18: Save Configuration

**What:** Save all changes to non-volatile memory.

**Command:**
```bash
write memory
```

**Alternative:**
```bash
end
copy running-config startup-config
```

**Verification:**
```
Building configuration...
[OK]
```

> **✅ Checkpoint:** Your configuration is now saved and will persist after reboot.

---

## 🔍 Verify SSH Configuration

### Step 19: Verify Configuration

**What:** Confirm that SSH is properly configured.

**Commands:**
```bash
show running-config | include hostname
show running-config | include ssh
show ip interface vlan 1
```

**Expected Output Examples:**
```
hostname SW1
ip domain-name lab.local
ip ssh version 2
Vlan1 is up, line protocol is up
  Internet address is 192.168.1.100/24
```

---

### Step 20: Test SSH Connection from Host

**What:** Connect to the switch via SSH from your host machine.

**Steps:**
1. Open PuTTY or your SSH client on the host computer
2. Enter the switch IP: `192.168.1.100`
3. Set port to `22` (SSH default)
4. Click **Open**
5. Login with: `admin` / `admin1234`

**Expected Result:**
```
Connection to 192.168.1.100 port 22
admin@192.168.1.100's password: ****
SW1#
```

> **💡 Tip:** Once SSH is working, you can use SSH instead of console access.

---

## 🆘 Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| **Can't connect via SSH** | SSH not enabled or IP not set | Run `show ip interface vlan 1` to verify IP; check `show running-config` for SSH settings |
| **Connection refused** | VTY lines not configured | Configure VTY lines and ensure `transport input ssh` is set |
| **Wrong password** | Incorrect credentials | Verify username and password with `show running-config \| include username` |
| **Can't reach IP address** | Routing issue | Ensure switch and host are on same network; test with `ping 192.168.1.100` |
| **SSH timeout** | Network disconnection | Check physical cable connection; verify network connectivity |

---

## ✅ Summary & Next Steps

### What You've Accomplished

- ✅ Created and added switch nodes to EVE-NG lab
- ✅ Configured basic switch settings (hostname, passwords)
- ✅ Set up management VLAN 1 with IP address
- ✅ Enabled SSH for secure remote access
- ✅ Created user account for SSH authentication
- ✅ Verified SSH connectivity from host machine

### Configuration Reference

```bash
# Quick reference for all commands
hostname SW1
enable secret cisco1234
line console 0
password cisco
login
exit

interface vlan 1
ip address 192.168.1.100 255.255.255.0
no shutdown
ip default-gateway 192.168.1.1
exit

ip domain-name lab.local
crypto key generate rsa
username admin privilege 15 secret admin1234

line vty 0 4
transport input ssh
login local
exit

write memory
```

### What's Next?

1. **Configure VLANs:**
   - Create multiple VLANs on your switches
   - Set access ports for each VLAN
   - Configure trunk ports between switches

2. **Inter-VLAN Routing:**
   - Configure a router for VLAN routing
   - Enable communication between VLANs

3. **Advanced Features:**
   - Configure Spanning Tree Protocol (STP)
   - Implement port security
   - Set up access control lists (ACLs)

4. **Best Practices:**
   - Document all configurations
   - Create backups of switch configurations
   - Test failover scenarios

---

## 📚 Useful Resources

- [Cisco Switch Configuration Guide](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst9200/software/release/17_3_1/configuration_guide/b_c9200_cg_173_1.pdf)
- [SSH Configuration Best Practices](https://www.cisco.com/c/en/us/support/docs/security/secure-shell-ssh/4145-ssh.html)
- [VLAN Configuration Guide](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst9200/software/release/17_3_1/configuration_guide/b_c9200_cg_173_1/configuring_vlans.pdf)
- [EVE-NG Documentation](https://www.eve-ng.net/)

---

✅ **Switch basic configuration completed successfully!** 🎓

**Ready to configure VLANs and advanced features! 🚀**
