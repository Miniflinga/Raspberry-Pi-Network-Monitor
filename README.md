# 📡 Raspberry Pi Network Monitoring (Prometheus + Grafana)

A lightweight, Docker-based network monitoring stack for continuously measuring internet stability, latency, and packet loss. Built for Raspberry Pi, optimized for diagnosing ISP issues.

---

## 🎯 Purpose

This project continuously monitors your internet connection by:

- Pinging `8.8.8.8` every second
- Logging latency and packet loss
- Storing time-series data in Prometheus
- Visualizing everything in Grafana dashboards

It is designed to help identify:
- ISP instability
- Packet loss patterns
- Latency spikes
- Time-of-day performance issues

---

## 🧱 Architecture
blackbox_exporter → Prometheus → Grafana

---

## 🧠 Why this setup
- **Prometheus** → industry-standard for time-series monitoring
- **blackbox_exporter** → handles ICMP pings without custom scripts
- **Grafana** → best-in-class visualization
- **Docker** → easy deployment and portability
- **Raspberry Pi** → low-cost 24/7 monitoring device

---

## 📦 Requirements

- Raspberry Pi (Pi 3/4/5 recommended)
- Docker
- Docker Compose

Install Docker (if needed):
```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

Install Docker Compose:
```bash
sudo apt install docker-compose -y
```

---

## 📁 Project structure
netmonitor/
├── docker-compose.yml
└── prometheus/
    └── prometheus.yml

---

## 📌 Goal
Turn your Raspberry Pi into a network monitoring probe that provides hard evidence of ISP performance issues.