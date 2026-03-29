# 🌐 Lab 3 — Dynamic Routing with OSPF

## 📋 Overview

Lab 3 focuses on dynamic routing using OSPF (Open Shortest Path First), configuring multi-router networks that automatically exchange routing information.

## 📄 Files

| File | Description |
|------|-------------|
| `Lab3.pdf` | Lab report and documentation |
| `lab3.pkt` | Cisco Packet Tracer simulation |

## 🎯 Learning Objectives

- Understand how OSPF discovers and shares routes dynamically
- Configure OSPF on multiple Cisco routers
- Verify OSPF neighbor relationships and routing tables
- Compare dynamic routing vs static routing

## 🛠️ Technologies

- Cisco Packet Tracer
- Cisco Routers (IOS)
- OSPF (Open Shortest Path First) — Area 0
- Serial / GigabitEthernet interfaces

## 📋 Key OSPF Commands

```bash
# Enable OSPF
router ospf 1
network 192.168.x.0 0.0.0.255 area 0

# Verify
show ip ospf neighbor
show ip route ospf
```

## 🚀 Getting Started

1. Open `lab3.pkt` in Cisco Packet Tracer
2. Follow the lab guide in `Lab3.pdf`
3. Verify all routers show OSPF neighbors in FULL state

---

📅 **Course:** Intergalactic Communications  
🎓 **Student ID:** 673380415-5
