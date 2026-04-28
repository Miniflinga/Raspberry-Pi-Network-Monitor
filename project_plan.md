# 📡 Project Plan

## 🚀 Quick Start

Want to get running immediately? Follow these 3 steps:

1. Create folder: `mkdir netmonitor && cd netmonitor && mkdir prometheus`
2. Copy the docker-compose.yml and prometheus.yml below
3. Run: `docker compose up -d`

Then access Grafana at `http://<pi-ip>:3000` (admin/admin)

---

## ⚙️ Setup

### 1. Create project folder
```bash
mkdir netmonitor && cd netmonitor
mkdir prometheus
```

### 2. docker-compose.yml
```yaml
version: "3.8"

services:
  blackbox:
    image: prom/blackbox-exporter
    container_name: blackbox
    restart: unless-stopped

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    restart: unless-stopped

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - "3000:3000"
    depends_on:
      - prometheus
    volumes:
      - grafana-data:/var/lib/grafana
    restart: unless-stopped

volumes:
  grafana-data:
```

### 3. prometheus/prometheus.yml
```yaml
global:
  scrape_interval: 1s

scrape_configs:
  - job_name: blackbox
    metrics_path: /probe
    params:
      module: [icmp]
    static_configs:
      - targets:
        - 8.8.8.8
      relabel_configs:
        - source_labels: [__address__]
          target_label: __param_target
        - source_labels: [__param_target]
          target_label: instance
        - target_label: __address__
          replacement: blackbox:9115
```

### 4. Run everything
```bash
docker compose up -d
```

---

## 🌐 Access services

**Grafana**
- URL: http://<your-pi-ip>:3000
- Login: admin / admin

**Prometheus**
- URL: http://<your-pi-ip>:9090

---

## 📊 Grafana setup

### Add data source
- Type: Prometheus
- URL: http://prometheus:9090

### Recommended PromQL queries

**Latency (ms):**
```promql
probe_duration_seconds{instance="8.8.8.8"} * 1000
```

**Packet loss (1 = success, 0 = loss):**
```promql
1 - avg_over_time(probe_success{instance="8.8.8.8"}[5m])
```

**Latency spikes:**
```promql
max_over_time(probe_duration_seconds{instance="8.8.8.8"}[5m]) * 1000
```

**Average latency over time:**
```promql
avg_over_time(probe_duration_seconds{instance="8.8.8.8"}[5m]) * 1000
```

---

## 🚨 Optional upgrades
- Alerts for packet loss > 2%
- Alerts for latency > 150ms
- Telegram / Discord notifications
- Multi-target monitoring (router vs internet vs DNS)
- Add AlertManager for alert routing

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

## 📌 Goal
Turn your Raspberry Pi into a network monitoring probe that provides hard evidence of ISP performance issues.