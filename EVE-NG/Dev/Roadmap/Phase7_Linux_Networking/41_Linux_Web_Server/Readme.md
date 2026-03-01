# 🖧 Lab 41: Linux Web Server (Apache & Nginx with SSL/TLS)

> Install and configure Apache2/Nginx web servers with SSL/TLS encryption.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 7 - Linux Networking |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 3-4 hours |

---

## 🎯 Lab Objectives
- ✅ Install Apache2 and Nginx web servers
- ✅ Configure virtual hosts for multiple domains
- ✅ Generate self-signed SSL/TLS certificates
- ✅ Configure HTTPS redirection
- ✅ Test web server access from clients

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Linux Basic Network | Lab 37 |
| Linux DNS Server | Lab 40 |

---

## 🗺️ Lab Topology
```
[Browser PC]──[SW]──[Router]──[Web Server 192.168.20.10]
                               Apache2/Nginx on port 80/443
```

---

## 🛠️ Configuration

### Install Apache2
```bash
apt-get install -y apache2
```

### Virtual Host Configuration
```bash
# /etc/apache2/sites-available/lab.local.conf
<VirtualHost *:80>
    ServerName www.lab.local
    DocumentRoot /var/www/lab
    Redirect permanent / https://www.lab.local/
</VirtualHost>

<VirtualHost *:443>
    ServerName www.lab.local
    DocumentRoot /var/www/lab
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/lab.crt
    SSLCertificateKeyFile /etc/ssl/private/lab.key
</VirtualHost>
```

### Self-Signed SSL Certificate
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/lab.key \
  -out /etc/ssl/certs/lab.crt
```

### Enable Site & SSL Module
```bash
a2enmod ssl
a2ensite lab.local
systemctl reload apache2
```

---

## ✅ Verification
```bash
systemctl status apache2
curl -k https://www.lab.local
openssl s_client -connect www.lab.local:443
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![Web Server](imgs/web_server.png)

---

## 📝 Summary
Apache2 and Nginx provide flexible web hosting with SSL/TLS support for secure HTTPS communication.
