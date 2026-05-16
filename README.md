<div align="center">

# 📡 InfraWatch — Production Server Monitoring Stack

### Prometheus + Grafana + Node Exporter · Docker · One Command Deploy

<p align="center">
  <img src="https://img.shields.io/badge/Docker_Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/Prometheus-Latest-E6522C?style=for-the-badge&logo=prometheus&logoColor=white" />
  <img src="https://img.shields.io/badge/Grafana-Latest-F46800?style=for-the-badge&logo=grafana&logoColor=white" />
  <img src="https://img.shields.io/badge/Node_Exporter-Powered-00BFFF?style=for-the-badge&logo=linux&logoColor=white" />
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" />
</p>

<p align="center">
  <img src="https://img.shields.io/github/stars/builtbysardor/infrawatch-monitoring-stack?style=flat-square" />
  <img src="https://img.shields.io/github/forks/builtbysardor/infrawatch-monitoring-stack?style=flat-square" />
  <img src="https://img.shields.io/badge/Setup_Time-Under_2_min-brightgreen?style=flat-square" />
  <img src="https://img.shields.io/badge/Platform-Linux%20%7C%20macOS-lightgrey?style=flat-square" />
</p>

<br/>

> **Production-ready observability platform** — Prometheus + Grafana + Node Exporter in Docker.
> From zero to a fully working monitoring dashboard in **one command**.

<br/>

```bash
docker compose up -d
```

**[🚀 Installation](#-installation) · [📸 Screenshots](#-screenshots) · [🎥 Demo Video](#-demo-video) · [🏗️ Architecture](#-architecture) · [🔧 Troubleshooting](#-troubleshooting)**

</div>

---

## 🎥 Demo Video

[![Watch InfraWatch Demo](https://img.shields.io/badge/▶_Watch_Demo-InfraWatch_Stack-FF0000?style=for-the-badge&logo=youtube)](https://github.com/builtbysardor/infrawatch-monitoring-stack)

> 📹 *To add a demo: record the Grafana dashboard, upload GIF to `presentation/demo.gif`:*
> `![Demo](https://raw.githubusercontent.com/builtbysardor/infrawatch-monitoring-stack/main/presentation/demo.gif)`

---

## 📸 Screenshots

### 📊 Grafana Dashboard — System Metrics Overview
> 💡 *Add screenshot: upload to `presentation/grafana-dashboard.png`*
> `![Grafana Dashboard](presentation/grafana-dashboard.png)`

### 🔥 Prometheus Targets
> *Add screenshot: `presentation/prometheus-targets.png`*

### 📈 Live CPU & Memory Graphs
> *Add screenshot: `presentation/metrics-graphs.png`*

---

## ✨ What's Included

| Service | Port | Description |
|---------|------|-------------|
| **Grafana** | 3000 | Visualization & dashboards |
| **Prometheus** | 9090 | Metrics collection & storage |
| **Node Exporter** | 9100 | Host system metrics |

---

## 🚀 Installation

### Prerequisites
- Docker & Docker Compose
- Linux / macOS / WSL2

### One-Command Deploy

```bash
# Clone the repo
git clone https://github.com/builtbysardor/infrawatch-monitoring-stack.git
cd infrawatch-monitoring-stack

# Start all services
docker compose up -d

# Check status
docker compose ps
```

### Access the Services

| Service | URL | Credentials |
|---------|-----|-------------|
| **Grafana** | http://localhost:3000 | admin / admin |
| **Prometheus** | http://localhost:9090 | — |
| **Node Exporter** | http://localhost:9100/metrics | — |

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────┐
│                  Docker Network                      │
│                                                      │
│  ┌────────────────┐    scrapes    ┌────────────────┐ │
│  │   Prometheus   │◄──────────────│ Node Exporter  │ │
│  │   :9090        │               │   :9100        │ │
│  └───────┬────────┘               └────────────────┘ │
│          │ data source                               │
│  ┌───────▼────────┐                                  │
│  │    Grafana     │                                  │
│  │    :3000       │◄─── You visit this               │
│  └────────────────┘                                  │
└──────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```
infrawatch-monitoring-stack/
├── grafana/
│   └── provisioning/
│       ├── dashboards/      # Pre-built dashboard JSONs
│       └── datasources/     # Prometheus data source config
├── prometheus/
│   └── prometheus.yml       # Scrape configuration
├── presentation/            # Screenshots & demo files
├── docker-compose.yml       # Main orchestration file
└── README.md
```

---

## 🔧 Troubleshooting

**Grafana shows no data?**
```bash
# Check Prometheus targets
curl http://localhost:9090/targets
# Ensure Node Exporter is running
docker compose logs node-exporter
```

**Port conflicts?**
Edit `docker-compose.yml` and change port mappings.

---

## 🔮 Roadmap

- [ ] 📧 Alertmanager — email & Slack alerts
- [ ] 🗄️ PostgreSQL monitoring
- [ ] 🐳 Container metrics (cAdvisor)
- [ ] 🔒 Grafana authentication setup
- [ ] 📱 Mobile-friendly dashboards
- [ ] ☸️ Kubernetes deployment option

---

## 🤝 Contributing

1. Fork the repo
2. Create your branch: `git checkout -b feature/new-dashboard`
3. Commit: `git commit -m 'Add new dashboard'`
4. Push: `git push origin feature/new-dashboard`
5. Open a Pull Request

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

<div align="center">

**Built with ❤️ by [Sardor](https://github.com/builtbysardor) · Samarkand, Uzbekistan 🇺🇿**

⭐ *Star this repo if it saved you time!*

</div>
