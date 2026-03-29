# 🔒 Lab 4 — NAT & Access Control Lists (ACLs)

## 📋 Overview

Lab 4 covers Network Address Translation (NAT) and Access Control Lists (ACLs) — essential tools for connecting private networks to the internet and enforcing traffic security policies.

## 📄 Files

| File | Description |
|------|-------------|
| `Lab 4.pdf` | Lab report and full documentation |

## 🎯 Learning Objectives

- Configure PAT (Port Address Translation / NAT Overload)
- Apply Standard and Extended ACLs on router interfaces
- Understand the difference between inbound and outbound ACL filtering
- Test NAT translations with `show ip nat translations`

## 🛠️ Technologies

- Cisco Packet Tracer / IOS
- NAT / PAT (Overload)
- Standard ACL — filter by source IP
- Extended ACL — filter by source, destination, protocol, port

## 📋 Key Commands

```bash
# NAT Configuration
ip nat inside source list NAT-ACL interface GigabitEthernet0/0 overload
ip access-list standard NAT-ACL
 permit 192.168.10.0 0.0.0.255

# Apply NAT on interfaces
interface GigabitEthernet0/0
 ip nat outside
interface GigabitEthernet0/1
 ip nat inside

# ACL Example (Extended)
ip access-list extended WAN-IN
 permit tcp 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 deny ip any any log

# Verify
show ip nat translations
show access-lists
```

---

📅 **Course:** Intergalactic Communications  
🎓 **Student ID:** 673380415-5
