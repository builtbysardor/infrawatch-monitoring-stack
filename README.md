# 📡 InfraWatch — Linux Server Monitoring Stack

> A production-ready, beginner-friendly monitoring system built with Docker, Prometheus, Node Exporter, and Grafana.  
> Spin up a complete observability platform with **one command**.

---

## 📋 Table of Contents

- [What is InfraWatch?](#what-is-infrawatch)
- [Architecture](#architecture)
- [What Each Component Does](#what-each-component-does)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [How to Run](#how-to-run)
- [Opening Grafana](#opening-grafana)
- [Default Login Credentials](#default-login-credentials)
- [Dashboard Panels](#dashboard-panels)
- [Useful Commands](#useful-commands)
- [Ports Reference](#ports-reference)
- [Troubleshooting](#troubleshooting)

---

## 🔍 What is InfraWatch?

**InfraWatch** is a complete server monitoring solution that gives you real-time visibility into your Linux server's health. It automatically collects system metrics (CPU, RAM, disk, network, uptime) and displays them in a beautiful Grafana dashboard — all running inside Docker containers.

Whether you're a beginner learning DevOps or a professional who needs a quick monitoring setup, InfraWatch gets you from zero to a fully working dashboard in under 2 minutes.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────┐
│                  Linux Server (Host)            │
│                                                 │
│  ┌──────────────────┐                           │
│  │  Node Exporter   │  ← Reads /proc, /sys      │
│  │  (port 9100)     │    exposes ~1000 metrics  │
│  └────────┬─────────┘                           │
│           │ HTTP scrape every 15s               │
│  ┌────────▼─────────┐                           │
│  │   Prometheus     │  ← Stores time-series     │
│  │   (port 9090)    │    data, runs queries      │
│  └────────┬─────────┘                           │
│           │ PromQL queries                      │
│  ┌────────▼─────────┐                           │
│  │    Grafana       │  ← Renders dashboards     │
│  │   (port 3000)    │    in your browser         │
│  └──────────────────┘                           │
│                                                 │
│  All containers run on an isolated Docker       │
│  bridge network called "monitoring"             │
└─────────────────────────────────────────────────┘
```

---

## 🧩 What Each Component Does

### 🔵 Node Exporter
Node Exporter is a lightweight agent that runs on your Linux server and **exposes system metrics over HTTP**. Think of it as a "sensor" attached to your server. It reads data directly from Linux kernel interfaces (`/proc`, `/sys`) and serves them in a format Prometheus can understand.

**Metrics it collects:**
- CPU usage per core and in total
- RAM: total, used, available, cached
- Disk: space used per partition, read/write speed
- Network: bytes sent/received per interface
- System load, uptime, open file descriptors, and hundreds more

**Endpoint:** `http://localhost:9100/metrics`

---

### 🟠 Prometheus
Prometheus is a **time-series database and monitoring system**. It regularly visits ("scrapes") Node Exporter's HTTP endpoint, reads all the metrics, timestamps them, and stores them efficiently on disk.

Prometheus uses its own query language called **PromQL** which lets you ask questions like:
- "What was the average CPU usage over the last 5 minutes?"
- "How much disk space is left on the root partition?"

Grafana uses PromQL queries to pull the data it needs for charts.

**Web UI:** `http://localhost:9090` — you can write PromQL queries directly here.

---

### 🟢 Grafana
Grafana is the **visualization layer**. It connects to Prometheus as a data source and uses PromQL queries to draw charts, graphs, and stat panels in a browser-based dashboard.

InfraWatch pre-configures both the Prometheus data source and the Linux monitoring dashboard automatically, so you don't need to click through any setup screens.

**Dashboard UI:** `http://localhost:3000`

---

### 🐳 Docker Compose
Docker Compose is the **orchestration tool** that reads `docker-compose.yml` and manages all three containers as a single application. It handles:
- Starting and stopping all services together
- Networking (all containers share a private bridge network)
- Volume persistence (data survives restarts)
- Environment variables and port mapping

---

## ✅ Prerequisites

Make sure the following are installed on your Linux/Ubuntu machine:

| Tool | Minimum Version | Check |
|------|----------------|-------|
| Docker | 20.x or newer | `docker --version` |
| Docker Compose | v2 (plugin) | `docker compose version` |

### Install Docker on Ubuntu (if not already installed):

```bash
# Update package index
sudo apt-get update

# Install Docker
sudo apt-get install -y docker.io docker-compose-plugin

# Add your user to the docker group (avoids needing sudo)
sudo usermod -aG docker $USER

# Apply the group change (or log out and back in)
newgrp docker

# Verify
docker --version
docker compose version
```

---

## 📁 Project Structure

```
infrawatch/
├── docker-compose.yml                          # Defines all 3 services
│
├── prometheus/
│   └── prometheus.yml                          # Scrape targets config
│
├── grafana/
│   ├── provisioning/
│   │   ├── datasources/
│   │   │   └── prometheus.yml                 # Auto-registers Prometheus
│   │   └── dashboards/
│   │       └── default.yml                    # Tells Grafana where dashboards are
│   └── dashboards/
│       └── linux-overview.json                # The pre-built dashboard
│
└── README.md                                   # This file
```

---

## 📦 Installation

```bash
# 1. Clone or download this project
git clone https://github.com/your-username/infrawatch.git
# — OR — simply copy the folder to your server

# 2. Enter the project directory
cd infrawatch
```

That's it. No additional installation steps needed — Docker handles everything else.

---

## ▶️ How to Run

### Start all services (detached/background mode):

```bash
docker compose up -d
```

Docker will:
1. Pull the latest images for Prometheus, Node Exporter, and Grafana
2. Create the isolated `monitoring` network
3. Start all 3 containers

### Verify all containers are running:

```bash
docker compose ps
```

Expected output:
```
NAME                          STATUS          PORTS
infrawatch_grafana            Up              0.0.0.0:3000->3000/tcp
infrawatch_node_exporter      Up              0.0.0.0:9100->9100/tcp
infrawatch_prometheus         Up              0.0.0.0:9090->9090/tcp
```

### View live logs:

```bash
docker compose logs -f
```

### Stop all services:

```bash
docker compose down
```

### Stop and delete all data (volumes):

```bash
docker compose down -v
```

---

## 🖥️ Opening Grafana

1. Open your browser and go to:
   ```
   http://localhost:3000
   ```
   *(If accessing a remote server, replace `localhost` with your server's IP address)*

2. Log in with the default credentials (see below)

3. The **InfraWatch — Linux Server Overview** dashboard loads automatically under:
   **Dashboards → InfraWatch → InfraWatch — Linux Server Overview**

---

## 🔐 Default Login Credentials

| Field | Value |
|-------|-------|
| Username | `admin` |
| Password | `infrawatch` |

> ⚠️ **Security note:** Change the default password immediately on any internet-facing server.  
> You can do this in Grafana: **Profile → Change Password**, or by updating the `GF_SECURITY_ADMIN_PASSWORD` environment variable in `docker-compose.yml`.

---

## 📊 Dashboard Panels

The pre-built dashboard includes the following panels:

| Panel | Type | Description |
|-------|------|-------------|
| 🖥️ System Uptime | Stat | How long the server has been running |
| ⚡ CPU Usage % | Stat + Graph | Current CPU load with color thresholds |
| 🧠 RAM Usage % | Stat + Graph | Memory utilization percentage |
| 💾 Disk Usage % | Stat | Root partition usage |
| 🌐 Network In | Stat | Inbound bytes per second |
| 📤 Network Out | Stat | Outbound bytes per second |
| ⚡ CPU Over Time | Time Series | Per-core CPU usage history |
| 🧠 RAM Over Time | Time Series | Used / Available / Total RAM over time |
| 🌐 Network Traffic | Time Series | Inbound and outbound traffic history |
| 💾 Disk by Partition | Bar Gauge | Usage % for all mounted filesystems |
| 📊 Disk I/O | Time Series | Read and write throughput |
| 🔧 Load Average | Time Series | System load at 1m, 5m, 15m intervals |

---

## 🛠️ Useful Commands

```bash
# Start the stack
docker compose up -d

# Stop the stack
docker compose down

# Restart a single service (e.g. after config changes)
docker compose restart prometheus

# View logs for a specific service
docker compose logs -f grafana
docker compose logs -f prometheus
docker compose logs -f node_exporter

# Check container resource usage
docker stats

# Reload Prometheus config without restarting
curl -X POST http://localhost:9090/-/reload

# Check Node Exporter raw metrics
curl http://localhost:9100/metrics | head -50

# Check Prometheus targets (are they UP?)
# Open in browser: http://localhost:9090/targets
```

---

## 🔌 Ports Reference

| Service | Port | URL | Purpose |
|---------|------|-----|---------|
| Grafana | 3000 | http://localhost:3000 | Dashboard UI |
| Prometheus | 9090 | http://localhost:9090 | Metrics DB + Query UI |
| Node Exporter | 9100 | http://localhost:9100/metrics | Raw metrics endpoint |

---

## 🔧 Troubleshooting

### Grafana shows "No data" on the dashboard
- Wait 1–2 minutes after startup for the first metrics to be scraped
- Visit http://localhost:9090/targets and check that `node_exporter` shows **UP**
- If it shows DOWN, run: `docker compose logs node_exporter`

### Cannot access Grafana on a remote server
- Make sure port 3000 is open in your firewall:
  ```bash
  sudo ufw allow 3000/tcp
  ```

### Metrics show host system as Docker container
- This is expected. Node Exporter mounts `/proc` and `/sys` from the host,  
  so metrics always reflect the physical/VM host, not the container itself.

### Containers keep restarting
```bash
# Check which container is failing
docker compose ps

# Inspect its logs
docker compose logs <service-name>
```

### "Permission denied" errors
```bash
# Make sure your user is in the docker group
sudo usermod -aG docker $USER && newgrp docker
```

---

## 📈 Extending InfraWatch

Want to monitor more services? You can easily add more scrape targets to `prometheus/prometheus.yml`:

```yaml
# Example: add a MySQL exporter
- job_name: "mysql"
  static_configs:
    - targets: ["mysql_exporter:9104"]
```

Popular exporters to add:
- **mysql_exporter** — MySQL / MariaDB metrics
- **redis_exporter** — Redis metrics
- **nginx-prometheus-exporter** — NGINX metrics
- **blackbox_exporter** — HTTP/TCP endpoint uptime checks

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

*Built with Sardor by the InfraWatch project.*
