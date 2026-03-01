# 🖧 Lab 49: SDN & OpenFlow (OpenDaylight Controller)

> Explore Software Defined Networking using OpenDaylight controller and OpenFlow protocol.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 9 - Modern Network |
| **Level** | ⭐⭐⭐⭐⭐ Expert |
| **Status** | 🔲 Todo |
| **Est. Time** | 5-6 hours |

---

## 🎯 Lab Objectives
- ✅ Deploy OpenDaylight (ODL) controller on Ubuntu
- ✅ Create virtual network topology with Mininet
- ✅ Understand OpenFlow flow tables
- ✅ Manage flows via ODL REST API
- ✅ Implement custom flow rules for traffic control

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Linux Basic Network | Lab 37 |
| Python Netmiko | Lab 43 |

---

## 🗺️ Lab Topology
```
[ODL Controller]──[Mgmt Network]
     ↕ OpenFlow
[Mininet Virtual Switches (OVS)]
  s1──s2──s3
  |       |
 h1      h2
```

---

## 🛠️ Configuration

### Install OpenDaylight
```bash
apt-get install -y openjdk-11-jdk
wget https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/karaf/0.14.4/karaf-0.14.4.tar.gz
tar -xzf karaf-0.14.4.tar.gz
cd karaf-0.14.4/bin
./karaf
# Install features
opendaylight-user@root> feature:install odl-l2switch-switch odl-restconf-all odl-mdsal-apidocs
```

### Install Mininet
```bash
apt-get install -y mininet
mn --controller=remote,ip=127.0.0.1,port=6633 --topo=linear,3
```

### ODL REST API - Add Flow
```bash
curl -X PUT \
  http://localhost:8181/restconf/config/opendaylight-inventory:nodes/node/openflow:1/table/0/flow/1 \
  -H 'Content-Type: application/json' \
  -u admin:admin \
  -d '{
    "flow": [{
      "id": "1",
      "priority": 100,
      "match": {"in-port": "1"},
      "instructions": {
        "instruction": [{
          "order": 0,
          "apply-actions": {
            "action": [{"output-action": {"output-node-connector": "2"}, "order": 0}]
          }
        }]
      }
    }]
  }'
```

---

## ✅ Verification
```bash
# Mininet
mininet> pingall
mininet> dpctl dump-flows

# ODL REST API
curl -u admin:admin http://localhost:8181/restconf/operational/opendaylight-inventory:nodes
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![ODL Dashboard](imgs/odl_dashboard.png)

---

## 📝 Summary
SDN separates control and data planes, enabling centralized programming of network behavior through OpenFlow.
