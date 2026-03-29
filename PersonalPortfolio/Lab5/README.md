# ⚙️ Lab 5 — Microservices File Processing Pipeline

## 📋 Overview

Lab 5 implements a microservices architecture deployed with Docker Compose, simulating an Internet-edge + ISP WAN scenario where multiple services handle file upload, processing, and AI analysis across a containerized LAN.

## 📄 Files

| File | Description |
|------|-------------|
| `Lab05_Report.pdf` | Full lab report and documentation |
| `docker-compose.yml` | Docker Compose stack definition |
| `automation/upload_service.py` | File upload endpoint (FastAPI, port 8000) |
| `automation/processing_service.py` | Text processing service (FastAPI, port 8001) |
| `automation/ai_service.py` | AI analysis / classification service (FastAPI, port 8002) |
| `automation/gateway_service.py` | API gateway orchestrating the full pipeline (FastAPI, port 9000) |
| `automation/start_services.py` | Service startup helper script |

---

## 🏗️ Architecture

```
Client
  │
  ▼
Gateway Service (port 9000)
  ├──► Upload Service   (port 8000)
  ├──► Processing Service (port 8001)
  └──► AI Service       (port 8002)
```

All services run inside a shared Docker bridge network (`lan_a`: `172.20.10.0/24`), simulating **LAN A (192.168.10.0/24)** in the lab topology.

---

## 🔧 Services

### 📤 Upload Service — `port 8000`
Receives file uploads, generates a unique `file_id`, and forwards metadata to the Processing Service.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Health check |
| `/upload` | POST | Upload a file (multipart/form-data) |

---

### ⚙️ Processing Service — `port 8001`
Receives file metadata, computes word/character counts, and forwards to the AI Service.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Health check |
| `/process` | POST | Process file metadata |

---

### 🤖 AI Service — `port 8002`
Performs mock sentiment analysis and file categorization based on filename and content preview.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Health check |
| `/analyze` | POST | Analyze file content |

**Supported categories:** `text_document`, `pdf_document`, `data_file`, `structured_data`, `source_code`, `log_file`

---

### 🚪 Gateway Service — `port 9000`
Orchestrates the full Upload → Processing → AI pipeline in a single request.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Aggregate health of all services |
| `/process-file` | POST | Full pipeline: upload + process + analyze |

---

## 🚀 Getting Started

### Prerequisites
- [Docker](https://www.docker.com/get-started) and Docker Compose installed

### Run the Stack

```bash
# Start all services
docker-compose up --build

# Verify all services are running
curl http://localhost:9000/health

# Upload and process a file
curl -X POST http://localhost:9000/process-file \
  -F "file=@yourfile.txt"
```

### Individual Service Health Checks

```bash
curl http://localhost:8000/health   # Upload Service
curl http://localhost:8001/health   # Processing Service
curl http://localhost:8002/health   # AI Service
curl http://localhost:9000/health   # Gateway (all services)
```

---

## 🛠️ Technologies

- **Python 3** — FastAPI, httpx, pydantic, uvicorn
- **Docker & Docker Compose** — containerization
- **REST APIs** — service-to-service communication

---

📅 **Course:** Intergalactic Communications  
🎓 **Student ID:** 673380427-8
