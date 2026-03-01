# 🖧 Lab 52: Network Monitoring (Zabbix, Grafana & InfluxDB)

> Build a complete network monitoring stack using Zabbix, Grafana, and InfluxDB.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 9 - Modern Network |
| **Level** | ⭐⭐⭐⭐ Advanced |
| **Status** | 🔲 Todo |
| **Est. Time** | 5-6 hours |

---

## 🎯 Lab Objectives
- ✅ Deploy Zabbix server for SNMP network monitoring
- ✅ Add network devices as Zabbix hosts
- ✅ Create custom dashboards in Grafana
- ✅ Store time-series metrics in InfluxDB
- ✅ Configure alerts for interface down/high CPU events

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Syslog & SNMP | Lab 18 |
| Linux Web Server | Lab 41 |
| NetBox | Lab 47 |

---

## 🗺️ Lab Topology
```
[R1, R2, SW1, SW2] ──SNMP──► [Zabbix Server]
                               InfluxDB
                               Grafana :3000
      [Syslog] ──UDP 514──► [Zabbix / Graylog]
```

---

## 🛠️ Configuration

### Enable SNMP on Cisco Devices
```ios
snmp-server community public RO
snmp-server community private RW
snmp-server host 192.168.100.30 version 2c public
snmp-server enable traps
```

### Install Zabbix (Docker Compose)
```bash
# docker-compose.yml
version: '3'
services:
  zabbix-server:
    image: zabbix/zabbix-server-mysql:latest
    environment:
      DB_SERVER_HOST: mysql-server
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix
    ports:
      - "10051:10051"

  zabbix-web:
    image: zabbix/zabbix-web-nginx-mysql:latest
    ports:
      - "8080:8080"

  mysql-server:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: zabbix

docker-compose up -d
# Access: http://192.168.100.30:8080 | Admin / zabbix
```

### Install Grafana + InfluxDB
```bash
# InfluxDB
docker run -d --name influxdb -p 8086:8086 influxdb:2.0

# Grafana
docker run -d --name grafana -p 3000:3000 grafana/grafana

# Access Grafana: http://192.168.100.30:3000 | admin / admin
```

### Zabbix - Add Network Device Host
```
1. Configuration → Hosts → Create Host
2. Host name: R1
3. Interfaces: SNMP | IP: 192.168.10.1 | Port: 161
4. Templates: Link "Template Net Cisco IOS SNMPv2"
5. Groups: Network Devices
```

### Grafana - Network Dashboard
```
1. Data Sources → Add → Zabbix
2. URL: http://zabbix-server:10051
3. Create Dashboard → Add Panel
4. Query: Interface traffic, CPU, Memory
5. Visualize as time-series graph
```

---

## ✅ Verification
```bash
# Test SNMP from Zabbix server
snmpwalk -v2c -c public 192.168.10.1 sysDescr

# Check Zabbix
docker logs zabbix-server

# Grafana
curl http://localhost:3000/api/health
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![Zabbix Dashboard](imgs/zabbix_dashboard.png)
![Grafana Dashboard](imgs/grafana_dashboard.png)
![Alert Notification](imgs/alert_notification.png)

---

## 📝 Summary
A complete monitoring stack provides real-time visibility into network health, performance metrics, and automated alerting.
