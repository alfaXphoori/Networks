# 🖧 Lab 36: QoS Basic (Traffic Shaping & Policing)

> Configure Quality of Service to prioritize and manage network traffic.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 6 - Advanced Switching |
| **Level** | ⭐⭐⭐⭐ Advanced |
| **Status** | 🔲 Todo |
| **Est. Time** | 3-4 hours |

---

## 🎯 Lab Objectives
- ✅ Classify traffic using class-maps (DSCP, ACL)
- ✅ Apply DSCP markings for traffic prioritization
- ✅ Configure traffic policing (rate limiting)
- ✅ Configure traffic shaping (buffering)
- ✅ Implement CBWFQ and LLQ queuing

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| MSTP | Lab 35 |
| ACL | Lab 20 |

---

## 🗺️ Lab Topology
```
[PC1]──[SW1]──[R1]──(WAN 1Mbps)──[R2]──[SW2]──[PC2]
              QoS Policy Applied Here
```

---

## 🛠️ Configuration

### R1 - Class Map
```ios
class-map match-any VOICE
 match dscp ef
class-map match-any VIDEO
 match dscp af41
class-map match-any DATA
 match dscp default
```

### R1 - Policy Map
```ios
policy-map WAN_QoS
 class VOICE
  priority 256
 class VIDEO
  bandwidth 512
 class DATA
  fair-queue
```

### R1 - Apply Policy
```ios
interface Serial0/0
 service-policy output WAN_QoS
```

---

## ✅ Verification
```ios
show policy-map interface Serial0/0
show class-map
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![QoS Stats](imgs/qos_stats.png)

---

## 📝 Summary
QoS ensures voice and critical traffic gets priority over bulk data during congestion.
