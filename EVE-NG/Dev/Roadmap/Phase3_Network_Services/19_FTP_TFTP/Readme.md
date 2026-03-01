# 🖧 Lab 19: FTP & TFTP Server

> Set up TFTP server for Cisco IOS backup/restore and FTP server for file transfers in an EVE-NG lab environment.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 3 - Network Services |
| **Level** | ⭐⭐ Beginner-Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 1-2 hours |

---

## 🎯 Lab Objectives
- ✅ Install and configure TFTP server on Linux
- ✅ Back up Cisco IOS running-config to TFTP server
- ✅ Restore configuration from TFTP server
- ✅ Install and configure FTP server (vsftpd)
- ✅ Transfer files between router and FTP server

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Routing | Lab 08 - Basic Routing |
| Linux Basics | Phase 7 - Linux Networking |
| IP Addressing | Lab 01 - Basic PC Cable |

---

## 🗺️ Lab Topology
```
  [R1]---[Switch]---[File Server 192.168.1.200]
                    TFTP: udp/69
                    FTP:  tcp/21
                    Ubuntu Linux
```

---

## 🛠️ Configuration

### TFTP Server Setup (Linux)
```bash
apt-get install tftpd-hpa -y
mkdir -p /tftpboot
chmod 777 /tftpboot

# /etc/default/tftpd-hpa
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/tftpboot"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_OPTIONS="--secure --create"

systemctl restart tftpd-hpa
systemctl enable tftpd-hpa
```

### Cisco Router - Backup to TFTP
```ios
copy running-config tftp:
! Enter: 192.168.1.200
! Filename: R1-config-2026-03-01.txt
```

### Cisco Router - Restore from TFTP
```ios
copy tftp: running-config
! Enter: 192.168.1.200
! Filename: R1-config-2026-03-01.txt
```

### FTP Server Setup (vsftpd)
```bash
apt-get install vsftpd -y

# /etc/vsftpd.conf key settings
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
chroot_local_user=YES
allow_writeable_chroot=YES

systemctl restart vsftpd
useradd -m ftpuser
passwd ftpuser
```

### Cisco Router - Copy via FTP
```ios
ip ftp username ftpuser
ip ftp password ftppass

copy running-config ftp:
! Enter: 192.168.1.200
! Filename: R1-backup.txt
```

---

## ✅ Verification
```ios
! Router verification
copy running-config tftp:
show flash:
dir flash:

! Linux verification
ls -la /tftpboot/
netstat -ulnp | grep 69
netstat -tlnp | grep 21
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab sets up TFTP and FTP servers on Linux for Cisco IOS configuration backup and restore operations, essential for network change management.
