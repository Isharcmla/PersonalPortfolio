# 🏢 Lab 6 — Enterprise Microservice Architecture with NAT & Observability

## 📋 Overview

Lab 6 implements a full enterprise branch network architecture combining Cisco IOS routing (OSPF, NAT, ACL, IP SLA) with a containerized microservice stack (Nginx gateway + backend services + log aggregation). Only the API Gateway is exposed to the internet via NAT port forwarding — backend services remain private.

## 📄 Files

| File | Description |
|------|-------------|
| `Lab06_Report.pdf` | Full lab report and documentation |
| `docker-compose.yml` | Full microservice stack |
| `gateway/nginx.conf` | Nginx API Gateway configuration |
| `service-a/app.py` | Backend Service A (Flask, port 9000) |
| `service-a/Dockerfile` | Docker build for Service A |
| `service-b/app.py` | Backend Service B (Flask, port 9001) |
| `service-b/Dockerfile` | Docker build for Service B |
| `observability/fluent-bit.conf` | Fluent Bit log aggregator config |
| `configs/R1-config.txt` | Cisco IOS config for R1 (NAT edge router) |
| `configs/R2-config.txt` | Cisco IOS config for R2 (remote router) |
| `scripts/validate.sh` | Automated validation test script |

---

## 🏗️ Architecture

```
Internet
   │
   ▼  NAT port 8000 only
  R1 (Edge Router — OSPF, NAT, ACL, IP SLA)
   │  192.168.10.1
   ▼
API Gateway (Nginx) — 192.168.10.10:8000  ← ONLY exposed service
   ├──► Service A (private) — 192.168.10.11:9000
   └──► Service B (private) — 192.168.10.12:9001

Log Collector (Fluent Bit) — 192.168.10.20

       Serial WAN (100.10.10.0/30)
   │
   ▼
  R2 — LAN B: 192.168.20.0/24
```

**Security principle:** Only the API Gateway port (`8000`) is NAT-forwarded. Backend services are private and inaccessible from the internet.

---

## 🌐 API Endpoints

### API Gateway (Nginx) — `port 8000`

| Route | Proxied To | Description |
|-------|-----------|-------------|
| `GET /health` | — | Gateway health check |
| `/api/service-a/*` | Service A :9000 | Route to Service A |
| `/api/service-b/*` | Service B :9001 | Route to Service B |
| Any other path | — | 403 Forbidden |

### Service A / Service B

| Endpoint | Description |
|----------|-------------|
| `GET /` | Service info & uptime |
| `GET /data` | Returns sample data records |

---

## 🔌 Cisco IOS Configuration (R1)

### Phase 1 — OSPF Dynamic Routing
```bash
router ospf 1
 router-id 1.1.1.1
 network 100.10.10.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
 default-information originate
```

### Phase 2 — IP SLA WAN Failure Detection
```bash
ip sla 1
 icmp-echo 8.8.8.8 source-interface GigabitEthernet0/0
 frequency 5
ip sla schedule 1 life forever start-time now
track 1 ip sla 1 reachability
ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/0 track 1
```

### Phase 3 — WAN Firewall ACL
```bash
ip access-list extended WAN-IN
 permit tcp 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 deny ip any any log
```

### Phase 4 — NAT Port Forwarding (API Gateway only)
```bash
ip nat inside source static tcp 192.168.10.10 8000 interface GigabitEthernet0/0 8000
```

---

## 🚀 Getting Started

### Run the Docker Stack

```bash
# Start all containers
docker-compose up --build -d

# Check gateway health
curl http://localhost:8000/health

# Access backend services via gateway
curl http://localhost:8000/api/service-a/
curl http://localhost:8000/api/service-b/data

# Confirm backend ports are NOT directly accessible
curl http://localhost:9000/  # Should fail (not exposed)
```

### Run Validation Tests

```bash
chmod +x scripts/validate.sh
./scripts/validate.sh
```

The script tests all 6 lab phases: OSPF, IP SLA, ACL firewall, NAT port forwarding, microservice routing, and observability.

---

## 🛠️ Technologies

- **Cisco IOS** — OSPF, NAT/PAT, ACL, IP SLA
- **Docker & Docker Compose** — containerized services
- **Nginx** — reverse proxy / API gateway
- **Python (Flask)** — backend microservices
- **Fluent Bit** — log collection and aggregation

---

📅 **Course:** Intergalactic Communications  
🎓 **Student ID:** 673380427-8
