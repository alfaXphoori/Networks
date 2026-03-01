# 🖧 Lab 42: Linux VPN Server (OpenVPN & WireGuard)

> Configure OpenVPN and WireGuard VPN servers for remote access connectivity.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 7 - Linux Networking |
| **Level** | ⭐⭐⭐⭐ Advanced |
| **Status** | 🔲 Todo |
| **Est. Time** | 4-5 hours |

---

## 🎯 Lab Objectives
- ✅ Install and configure OpenVPN server
- ✅ Generate PKI certificates for OpenVPN
- ✅ Connect VPN client and verify tunnel
- ✅ Configure WireGuard as a modern VPN alternative
- ✅ Compare OpenVPN vs WireGuard performance

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Linux as Router | Lab 38 |
| Linux Web Server | Lab 41 |

---

## 🗺️ Lab Topology
```
[VPN Client]──[Internet]──[VPN Server 203.x.x.x]──[Internal LAN 10.0.0.0/24]
                           OpenVPN: tun0 10.8.0.1
                           WireGuard: wg0 10.9.0.1
```

---

## 🛠️ Configuration

### OpenVPN Server
```bash
apt-get install -y openvpn easy-rsa

# Generate PKI
make-cadir ~/openvpn-ca && cd ~/openvpn-ca
./easyrsa init-pki
./easyrsa build-ca
./easyrsa gen-req server nopass
./easyrsa sign-req server server
./easyrsa gen-dh

# /etc/openvpn/server.conf
port 1194
proto udp
dev tun
ca ca.crt
cert server.crt
key server.key
dh dh.pem
server 10.8.0.0 255.255.255.0
push "route 10.0.0.0 255.255.255.0"
```

### WireGuard Server
```bash
apt-get install -y wireguard
wg genkey | tee /etc/wireguard/privatekey | wg pubkey > /etc/wireguard/publickey

# /etc/wireguard/wg0.conf
[Interface]
PrivateKey = <server-private-key>
Address = 10.9.0.1/24
ListenPort = 51820
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

[Peer]
PublicKey = <client-public-key>
AllowedIPs = 10.9.0.2/32
```

---

## ✅ Verification
```bash
# OpenVPN
systemctl status openvpn@server
ip addr show tun0

# WireGuard
wg show
ip addr show wg0
ping -c 4 10.0.0.1
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![VPN Tunnel](imgs/vpn_tunnel.png)

---

## 📝 Summary
OpenVPN provides mature PKI-based VPN while WireGuard offers a simpler, faster modern alternative.
