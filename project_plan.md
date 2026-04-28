# 📡 Project Plan

## ⚙️ Setup

### 1. Create project folder ✅
```bash
mkdir netmonitor && cd netmonitor
mkdir prometheus
```

### 2. docker-compose.yml ✅
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

### 3. prometheus/prometheus.yml ✅
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

### 4. Run everything ✅
```bash
docker compose up -d
```

---

## 5. Access services ✅

**Grafana**
- URL: http://<your-pi-ip>:3000
- Login: admin / admin

**Prometheus**
- URL: http://<your-pi-ip>:9090

---

## 6. Grafana setup ✅

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
- Alerts for latency > 150ms
- Alerts for packet loss > 2%
- Telegram / Discord notifications
- Multi-target monitoring (router vs internet vs DNS)
- Add AlertManager for alert routing