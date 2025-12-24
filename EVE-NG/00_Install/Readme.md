# 🚀 EVE-NG Installation Guide

> Complete step-by-step guide to install and configure EVE-NG (Emulated Virtual Environment - Network Graphical) for network labs and simulations.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Prerequisites & Downloads](#prerequisites--downloads)
2. [Windows Environment Setup](#windows-environment-setup)
3. [Virtual Machine Setup](#virtual-machine-setup)
4. [EVE-NG Installation](#eve-ng-installation)
5. [Post-Installation Configuration](#post-installation-configuration)
6. [Web Interface Access](#web-interface-access)

---

## 📥 Prerequisites & Downloads

Before starting the installation, download the required software:

| Software | Purpose | Link |
|----------|---------|------|
| **VirtualBox** | Hypervisor for running virtual machines | [Download](https://www.virtualbox.org/) |
| **VMWare** | Hypervisor for running virtual machines | [Download](https://drive.google.com/file/d/149EBZ2zX4P6vws69DXrD-YWwnsTeMJZw/view?usp=sharing)|
| **EVE-NG ISO** | Network lab environment OS image | [Download](https://www.eve-ng.net/index.php/download/) |

> **⚠️ Important:** Ensure you have at least 8 GB RAM and 60 GB free disk space on your computer.

---

## 🖥️ Windows Environment Setup

> **Purpose:** Disable conflicting Windows features that interfere with VirtualBox and virtualization.

### Step 0: Turn ON Virtualization in BIOS

**What:** Enable hardware virtualization support in your BIOS/UEFI.

**How to:**
1. Restart your computer
2. Enter BIOS settings (usually by pressing **F2**, **F10**, **Del**, or **Esc** during boot)
3. Look for **Virtualization Technology** (Intel VT-x) or **SVM Mode** (AMD-V)
4. **Enable** the virtualization option
5. Save changes and exit BIOS

> **💡 Tip:** If you can't find the setting, consult your motherboard manual or search online for your specific model.

---

### Step 1: Disable Hypervisor Launch Type

**What:** Disable Windows Hypervisor to avoid conflicts with VirtualBox.

**How to:**
1. Press **Windows Key + X** and select **Windows PowerShell (Admin)**
2. Copy and paste this command:
```bash
bcdedit /set hypervisorlaunchtype off
```
3. Press **Enter**
4. Restart your computer

![Disable Hypervisor](imgs/powShell.png)

> **Note:** You'll need to restart your computer for this change to take effect.

---

### Step 2: Disable Group Policy Restrictions

**What:** Remove Device Guard security policies that restrict virtualization.

**How to:**
1. Press **Windows Key + R**
2. Type `gpedit.msc` and press **Enter**
3. Navigate to: **Computer Configuration** → **Administrative Templates** → **System** → **Device Guard**
4. Find and **Disable**:
   - **Deploy App Control for Business**
   - **Turn On Virtualization Base Security**
5. Click **Apply** and **OK**

![Disable Policy](imgs/editGroup.png)

> **Note:** If you don't have Home edition of Windows, you may not have Group Policy Editor. Skip this step if unavailable.

---

### Step 3: Turn Off Memory Integrity

**What:** Disable Kernel DMA Protection that can interfere with VM performance.

**How to:**
1. Press **Windows Key** and type `core isolation`
2. Select **Core isolation** from the results
3. Click **Core isolation details**
4. Toggle **Memory integrity** to **OFF**

![Disable Memory Integrity](imgs/memIntegrity.png)

> **⚠️ Warning:** This reduces security, only do it if needed for VM performance.

---

### Step 4: Disable Windows Features

**What:** Turn off Hyper-V and related features to avoid conflicts.

**How to:**
1. Press **Windows Key + R**
2. Type `optionalfeatures` and press **Enter**
3. Uncheck the following (if present):
   - ☐ **Hyper-V**
   - ☐ **Virtual Machine Platform**
   - ☐ **Windows Hypervisor Platform**
4. Click **OK**
5. Restart your computer when prompted

![Disable Windows Features](imgs/winFeature.png)

> **✅ Checkpoint:** After completing all Windows setup steps, restart your computer before proceeding.

---

## 🔧 Virtual Machine Setup

> **Purpose:** Create and configure a virtual machine in VirtualBox with proper resources for EVE-NG.

### Step 0: Create New Virtual Machine

**What:** Create a new virtual machine container.

**How to:**
1. Open **VirtualBox**
2. Click the **New** button in the toolbar
3. Proceed to the next step

![New Virtual Machine](imgs/_0New%20Virtual.png)

---

### Step 1: Set Virtual Machine Name and Type

**What:** Name your VM and select the operating system type.

**How to:**
1. Set the VM name: **EVE-NG**
2. Select **Type**: **Linux**
3. Select **Version**: **Ubuntu (64-bit)** (or similar Linux version)
4. Click **Continue**

![Create Name](imgs/_01Create%20Name.png)

---

### Step 2: Go to Settings

**What:** Access the VM settings to configure resources.

**How to:**
1. In VirtualBox, right-click on the **EVE-NG** virtual machine
2. Select **Settings**

![Go Setting](imgs/_02Go%20Setting.png)

---

### Step 3: Add CPU and RAM

**What:** Allocate sufficient memory and processing power.

**Configuration:**
- Go to **System** tab
- Set **Base Memory (RAM)**: **4096 MB (4 GB minimum)**
- Set **Processors (CPU)**: **2-4 Cores** (depending on your host)

> **Recommended:**
> - RAM: 4-8 GB
> - CPU: 2-4 cores
> - Adjust based on your host computer's resources

![Add CPU RAM](imgs/_03Add%20CPU%20RAM.png)

---

### Step 4: Enable VT-x/AMD-V

**What:** Enable hardware virtualization acceleration.

**How to:**
1. Still in **System** tab
2. Click the **Acceleration** tab
3. Check **Enable VT-x/AMD-V** (should be available if BIOS is enabled)

![Open VT](imgs/_04Open%20VT.png)

---

### Step 5: Add Hard Disk

**What:** Create and attach storage for the VM.

**How to:**
1. Go to **Storage** tab
2. Under **Storage Devices**, add a **Hard Disk** (minimum **30-50 GB**)
3. Attach the **EVE-NG ISO file** to the optical drive
4. Click **OK** to save settings

![Add HDD](imgs/_05Add%20HDD.png)

---

### Step 6: Configure Network

**What:** Set up network connectivity.

**How to:**
1. Go to **Network** tab
2. Set **Attached to**: **Bridged Adapter**
3. Click **OK** to save

![Add Network](imgs/_06Add%20Network.png)

> **ℹ️ Note:** Bridged mode allows the VM to appear on your local network with its own IP address.

---

## 📦 EVE-NG Installation

> **Purpose:** Install the EVE-NG operating system on the virtual machine.

### Step 7: Start Virtual Machine

**What:** Boot the VM and begin the installation.

**How to:**
1. In VirtualBox, click the **EVE-NG** VM
2. Click the **Start** button (green arrow)

![Start](imgs/_07Start.png)

---

### Step 8: Choose Language

**What:** Select installation language.

**How to:**
1. When the bootloader appears, select your preferred language
2. **English** is recommended (easier to follow guides)

![Choose Language](imgs/_08Choose%20laugaue.png)

---

### Step 9: Proceed with Installation

**What:** Begin the EVE-NG system installation.

**How to:**
1. Select **Install** from the menu
2. Follow the on-screen prompts

![Go Next](imgs/_09Go%20next.png)

---

### Step 10: Complete Installation

**What:** Wait for the installation process to finish.

**How to:**
1. The installer will copy files and configure the system
2. This may take 5-10 minutes
3. When prompted, click **Continue**

![Continue](imgs/_11Continue.png)

---

### Step 11: Unmount CD/ISO

**What:** Remove the installation media from the virtual CD drive.

**How to:**
1. Go to **Devices** menu → **Optical Drives** → **Remove disk from virtual drive**
2. Or right-click the CD icon in the VM status bar

![Unmount CD](imgs/_11Unmount%20CD.png)

---

### Step 12: Confirm Unmount

**What:** Confirm the removal of the ISO file.

**Action:** Click **OK** or confirm when prompted

![Confirm Unmount](imgs/_12Confirm%20Unmount.png)

---

### Step 13: Shut Down the VM

**What:** Properly shut down the machine after initial setup.

**How to:**
1. Use **Machine** → **Power Off** or
2. Right-click and select **Power Off** → **Force Power Off**

![Force Power Off](imgs/_13Force%20PoweOff.png)

---

## ⚙️ Post-Installation Configuration

> **Purpose:** Configure EVE-NG system settings on first boot.

### Step 14: First Login

**What:** Log in to EVE-NG for the first time.

**Credentials:**
- **Username**: `root`
- **Password**: `eve`

**How to:**
1. Restart the VM (it should boot automatically)
2. At the login prompt, enter the credentials above

![First Login](imgs/_14First%20login.png)

---

### Step 15: Change Root Password

**What:** Set a secure password for the root user.

**How to:**
1. You'll be prompted to change the root password
2. Enter a new secure password
3. Remember this password for future logins

![Password](imgs/_15Passwd.png)

> **Security Tip:** Use a strong password with uppercase, lowercase, numbers, and special characters.

---

### Step 16: Confirm Password

**What:** Verify the new password.

**How to:**
1. Re-enter the new password to confirm
2. Press **Enter**

![Confirm Password](imgs/_16Confirm%20Passwd.png)

---

### Step 17: Set Hostname

**What:** Give your EVE-NG system a network name.

**How to:**
1. Enter a hostname (e.g., `eve-ng-lab`, `networking-lab`)
2. Press **Enter**

![Hostname](imgs/_17Hostname.png)

> **ℹ️ Note:** The hostname is used for network identification.

---

### Step 18: Configure DNS Domain Name

**What:** Set the DNS domain for your system.

**How to:**
1. Enter your DNS domain (e.g., `lab.local`, or leave blank)
2. Press **Enter**

![DNS](imgs/_18DNS.png)

> **⚠️ Optional:** You can leave this blank if you don't have a specific domain.

---

### Step 19: Configure IP Address (DHCP)

**What:** Set up network connectivity.

**How to:**
1. Choose **DHCP** for automatic IP assignment (recommended for beginners)
2. Or configure a **Static IP** if your network requires it
3. Press **Enter**

![DHCP](imgs/_19DHCP.png)

> **💡 Tip:** DHCP is easier and works well for lab environments.

---

### Step 20: Configure NTP Server

**What:** Set up time synchronization.

**How to:**
1. Enter an NTP server (e.g., `pool.ntp.org`) or leave blank
2. Press **Enter**

![NTP](imgs/_20NTP.png)

> **✅ Default:** Leaving blank uses system default NTP settings.

---

### Step 21: Configure Proxy

**What:** Set up proxy if your network requires it.

**How to:**
1. If your network requires a proxy, enter the proxy address
2. Otherwise, leave blank and press **Enter**
3. Select **Direct Connection** option

![Proxy](imgs/_21Proxy.png)

---

## 🌐 Network Configuration

> **Purpose:** Understand and configure the network setup for EVE-NG to ensure proper connectivity.

### EVE-NG Network Overview

**What:** EVE-NG can be accessed from your host machine or other devices on the network.

**Network Architecture:**
- **VirtualBox Network**: Bridged Adapter (VM appears on local network)
- **Host-Only Network**: Access only from host machine
- **NAT Network**: VM can access external networks through host

**Step-by-Step Network Configuration:**

![GoNetwork](imgs/_24go_Network.png)

> **Step 1 - Go to Network Settings:**
> Click on the VirtualBox menu and navigate to **File** → **Preferences** or **Settings** to access network configuration options.

---

![Create Network Adapter](imgs/_25CreateNetwork.png)

> **Step 2 - Create New Network Adapter:**
> In the Network settings, click the **+** button or **Create** to add a new network adapter for your VM.

---

![Done Network Adapter](imgs/_26DoneNetwork.png)

> **Step 3 - Adapter Created Successfully:**
> The new network adapter is now created and ready for configuration. You'll see it listed with default settings.

---

![Setting Network Adapter](imgs/_27SettingMachine.png)

> **Step 4 - Configure Adapter Settings:**
> Verify the adapter type (should be **Host-Only** or **Bridged** depending on your needs). Check the IPv4 and DHCP settings to ensure proper network range.

---

![config Network Adapter](imgs/_28ConfigNetwork.png)

> **Step 5 - Final Configuration:**
> Set the DHCP server range and network mask. Typical configuration:
> - **Network**: 192.168.56.0/24
> - **DHCP Range**: 192.168.56.101 - 192.168.56.254
> - **Server**: 192.168.56.1  

**Configuration Options:**

| Option | Benefit | Use Case |
|--------|---------|----------|
| **Bridged Adapter** | VM gets IP from network | Access from any device on network |
| **Host-Only** | Isolated to host machine | Secure, local-only access |
| **NAT Network** | VM can access internet | External connectivity needed |

**Network Adapter Configuration (From VirtualBox Settings):**


---

### Step 22: Installation Complete

**What:** System configuration is finished.

**Status:** All configuration steps are now complete.

![Done](imgs/_22Done.png)

> **✅ Checkpoint:** Your EVE-NG system is now configured and ready to use.

---

## 🌐 Web Interface Access

### Step 23: Access EVE-NG Web Interface

**What:** Connect to EVE-NG using a web browser.

**How to:**
1. Open a web browser on your host machine (not the VM)
2. Enter the EVE-NG IP address in the address bar
   - You can find the IP by running `ifconfig` or `ip addr` in the EVE-NG terminal
3. Default address pattern: `http://192.168.x.x` (depends on your network)

**Credentials:**
- **Username**: `admin`
- **Password**: `eve`

![Login Web](imgs/_23LoginWeb.png)

> **💡 Tip:** The web interface is where you'll create and manage your network labs.

---

### Step 24: Install Windows Client Side (Optional)

**What:** Install client tools for enhanced integration with Windows.

**How to:**
1. Download **Windows Client Side** from [EVE-NG Downloads](https://www.eve-ng.net/index.php/download/)
2. Run the installer on your Windows host machine
3. This enables better console integration and performance

![Windows Client Pack](imgs/winPack.png)

> **ℹ️ Note:** This is optional but recommended for better integration.

---

## ✅ Summary & Next Steps

**Congratulations!** You have successfully:
- ✅ Configured Windows for virtualization
- ✅ Created and configured a VirtualBox VM
- ✅ Installed EVE-NG operating system
- ✅ Configured network and system settings
- ✅ Accessed the EVE-NG web interface

### What's Next?

1. **Create Your First Lab:**
   - Log in to the web interface
   - Create network nodes (routers, switches, etc.)
   - Start building your network topology

2. **Load Network Images:**
   - Upload router and switch images (Cisco, Juniper, etc.)
   - These are required to run network devices in your labs

3. **Explore Documentation:**
   - Visit [EVE-NG Documentation](https://www.eve-ng.net/)
   - Join the [community forums](https://www.eve-ng.net/index.php/community)

4. **Practice Labs:**
   - Start with basic routing configurations
   - Progress to advanced protocols (OSPF, BGP, MPLS)
   - Build real-world network scenarios

---

## 🆘 Troubleshooting

| Issue | Solution |
|-------|----------|
| **Can't access VM** | Check if VM is running; restart VirtualBox |
| **Network not working** | Verify Bridged Adapter is selected and enabled |
| **VM is slow** | Increase RAM/CPU allocation in VM settings |
| **Can't access web interface** | Find VM IP with `ifconfig`; ensure firewall allows traffic |
| **Login fails** | Use default credentials: `admin/eve` or `root/eve` |

---

## 📚 Useful Resources

- [EVE-NG Official Website](https://www.eve-ng.net/)
- [VirtualBox Documentation](https://www.virtualbox.org/manual/UserManual.html)
- [Network Lab Basics](https://www.eve-ng.net/index.php/documentation/)
- [Community Forum](https://www.eve-ng.net/index.php/community)

---

✅ **Installation and configuration completed successfully!** 🚀

**Happy Labbing! 🎓**







