# 📊 Linux Node Monitoring with Prometheus + Grafana + Node Exporter (Dockerized)

A simple, effective monitoring stack using **Docker Compose** to monitor system-level metrics in real-time. This project sets up:

- ✅ Node Exporter: to expose Linux system metrics  
- ✅ Prometheus: to collect and store those metrics  
- ✅ Grafana: to visualize metrics in customizable dashboards  

---

## 📦 Tech Stack Overview

| Component       | Port  | Description                        |
|----------------|-------|------------------------------------|
| Prometheus     | 9090  | Metrics collector                  |
| Node Exporter  | 9100  | Linux system metrics exporter      |
| Grafana        | 3000  | Beautiful dashboard UI             |

---

## 🛠️ Prerequisites

Before you begin:

- Docker & Docker Compose installed  
- Internet connection (to pull official images)  
- Ports `3000`, `9090`, and `9100` available (or modify as needed)

---

## 🧾 Project Structure

```
node-exporter-docker/
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml
└── README.md
```

---

## 🧩 Step-by-Step Setup

### 1️⃣ Create `docker-compose.yml`

```yaml
version: '3'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - ./prometheus:/etc/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
    ports:
      - "9090:9090"

  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    ports:
      - "9100:9100"

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
```

---

### 2️⃣ Create `prometheus.yml` under `/prometheus/`

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']
```

---

### 3️⃣ Start the Stack

```bash
docker-compose up -d
```

✔ This will spin up Prometheus, Node Exporter, and Grafana in detached mode.

To stop all services:

```bash
docker-compose down
```

---

## 🌐 Access the Services

| Service     | URL                      | Notes                   |
|-------------|--------------------------|--------------------------|
| Prometheus  | http://localhost:9090     | Metric browser and query |
| Grafana     | http://localhost:3000     | Default login: `admin` / `admin` |

---

## 📈 Import Grafana Dashboard (Node Exporter Full)

1. Open Grafana → `+ Create` → `Import`
2. Enter Dashboard ID: **1860**
3. Click **Load**
4. Select **Prometheus** as the data source
5. Click **Import**

🎉 You'll now see real-time metrics for:

- CPU usage per core
- Memory usage
- Disk I/O
- Network stats
- System uptime

---

## ❗ Common Errors & Fixes

### 🔴 Port 9100 already in use

**Error:**
```
Bind for 0.0.0.0:9100 failed: port is already allocated
```

**Fix:**
```bash
# Find which process is using it
netstat -aon | findstr :9100  # Windows
lsof -i :9100                 # Linux/macOS

# Either stop the process OR change the port in docker-compose.yml
```

---

### ❌ `curl localhost:9100/metrics` shows "426 Upgrade Required"

**Reason:** You may be using a macOS (Darwin) Node Exporter binary on a Windows system.

**Fix:**
- Download the correct `.windows-amd64.zip` binary from the [Node Exporter releases](https://prometheus.io/download/#node_exporter) page

---

### ❌ Can't find "Import Dashboard" in Grafana

**Fix:**
- Make sure you're on the **Grafana homepage**
- Use the left sidebar → `+ Create` → `Import`
- Don't try to import from within an existing dashboard panel

---

## 🧠 Pro Tips

- View Prometheus targets at: http://localhost:9090/targets  
- To persist Grafana dashboards across restarts, **add a volume** for Grafana's data directory
- You can save/export Grafana dashboards as JSON and re-import them later

---

## 👨‍💻 Contributors

- **Rahul** – Setup, testing, documentation

---

## 🗃️ Optional: Add Persistent Grafana Storage

To avoid losing dashboards after a restart, add a volume to Grafana in `docker-compose.yml`:

```yaml
grafana:
  ...
  volumes:
    - grafana-storage:/var/lib/grafana

volumes:
  grafana-storage:
```

---

---

## 📜 License

This project is open-source and available for use and modification under the [MIT License](LICENSE).

---

