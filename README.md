# PPRD Systems: Observability Infrastructure (WIP)

> **Empowering Modern Engineering with Scalable Observability**  
> *Innovating across Healthcare, IoT, and Fintech domains.*

This repository provides a **ready-to-run observability stack** for **PPRD Systems projects** and open-source developers who want a quick, unified way to monitor distributed systems.  
It bundles **OpenTelemetry**, **Prometheus**, **Grafana**, **Loki**, and **Tempo** allowing you to collect, visualize, and analyze logs, metrics, and traces locally or in the cloud.

> Initially designed for **local deployment** the initial development and testing phases, but it can easily evolve into a **production-grade cloud observability system** suitable for microservices, IoT platforms, Healthcare systems, event-driven architectures, and others.


> # ⚠️ Disclaimer
> This repository is part of an ongoing **research and experimental development projects** conducted by **PPRD Systems, LLC** for educational and exploratory purposes only.
>  
> ⚖️ **Liability & Usage**
Use of this repository or its components is **strictly at the user’s own risk**.
**PPRD Systems, LLC** provides all materials **“as is,” without any warranties**, express or implied.
> 
> © **2025 PPRD Systems, LLC**. All rights reserved


## Overview

This stack enables:
This stack aligns with **PPRD Systems’ modular infrastructure strategy**, providing:
- **Unified observability** with traces, metrics, and logs.
- **Local telemetry ingestion** using OpenTelemetry Collector from microservices and event-driven systems.
- **Grafana-based dashboards and visualizations** for real-time visibility. (upcoming)
- **Flexible extension and Scalable architecture** for both local and enterprise-grade environments monitoring. (upcoming)

Our goal is to help teams **observe everything that matters** — from device telemetry to backend performance without setup friction.

## Stack Components

| Service | Description | Default Port |
|----------|--------------|---------------|
| **OpenTelemetry Collector** | Central telemetry collector receiving metrics, logs, and traces from applications. | `4317` |
| **Prometheus** | Metrics storage and alerting system scraping data from the collector, alerting, and query system. | `9090` |
| **Loki** | Lightweight, cost-efficient log aggregation system optimized for Grafana. | - |
| **Tempo** | Distributed tracing backend compatible with OpenTelemetry. | - |
| **Grafana** | Unified visualization interface connecting all observability sources. | `3000` |

## Docker Compose Setup

This repository includes a `docker-compose.yml` that orchestrates all services:

```yaml
services:
  otel-collector:
    image: otel/opentelemetry-collector-contrib:${OTEL_IMAGE_VERSION:-0.133.0}
    container_name: ${OTEL_CONTAINER_NAME:-otel-collector}
    command: ["--config=/etc/otel/collector-config.yml"]
    volumes:
      - ./volumes/otel/collector-config.yaml:/etc/otel/collector-config.yml
    ports:
      - "4317:4317"
  
  loki:
    image: grafana/loki:${LOKI_IMAGE_VERSION:-3.5.3}
    container_name: ${LOKI_CONTAINER_NAME:-loki}
    command: -config.file=/etc/loki/loki-config.yaml
    volumes:
      - ./volumes/loki/loki.yaml:/etc/loki/loki-config.yaml
    depends_on:
      - otel-collector
  
  tempo:
    image: grafana/tempo:${TEMPO_IMAGE_VERSION:-2.8.2}
    container_name: ${TEMPO_CONTAINER_NAME:-tempo}
    command: ["-config.file=/etc/tempo.yaml"]
    volumes:
      - ./volumes/tempo/tempo.yaml:/etc/tempo.yaml
    depends_on:
      - otel-collector
  
  prometheus:
    image: prom/prometheus:${PROMETHEUS_IMAGE_VERSION:-v3.5.0}
    container_name: ${PROMETHEUS_CONTAINER_NAME:-prometheus}
    volumes:
      - ./volume/prometheus/prometheus.yaml:/etc/prometheus/prometheus.yaml
    command:
      - --config.file=/etc/prometheus/prometheus.yaml
    ports:
      - "9090:9090"
    depends_on:
      - otel-collector
  
  grafana:
    image: grafana/grafana:${GRAFANA_IMAGE_VERSION:-12.0}
    container_name: ${GRAFANA_CONTAINER_NAME:-grafana}
    volumes:
      - ./volumes/grafana/datasources:/etc/grafana/provisioning/datasources
      - ./volumes/grafana/dashboards:/etc/grafana/provisioning/dashboards
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=admin
    depends_on:
      - loki
      - prometheus
      - tempo
```

## Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/pprds-public/observability-infrastructure.git
cd observability-infrastructure
```

### 2. Configure Environment Variables
You can override container names or image versions via a `.env` file:
```bash
OTEL_IMAGE_VERSION=0.133.0
OTEL_CONTAINER_NAME=otel-collector
LOKI_IMAGE_VERSION=3.5.3
LOKI_CONTAINER_NAME=loki
TEMPO_IMAGE_VERSION=2.8.2
TEMPO_CONTAINER_NAME=tempo
PROMETHEUS_IMAGE_VERSION=v3.5.0
PROMETHEUS_CONTAINER_NAME=prometheus
GRAFANA_IMAGE_VERSION=12.0
GRAFANA_CONTAINER_NAME=grafana
```

### 3. Start the Observability Stack
```bash
docker compose up -d
```

### 4. Access Dashboards
- **Grafana:** [http://localhost:3000](http://localhost:3000)  
  Login: `admin / admin`  
- **Prometheus:** [http://localhost:9090](http://localhost:9090)

## Directory Structure

```
observability-infrastructure/
├── docker-compose.yml
└── volumes/
    ├── grafana/
    │   ├── datasources/
    │   └── dashboards/
    ├── loki/
    ├── otel/
    ├── prometheus/
    └── tempo/
```

## Future Plans

- Add **Kubernetes manifests** or **Helm charts**.
- Integrate with **cloud-managed telemetry pipelines**.
- Provide **Terraform templates** for provisioning.
- Extend **Grafana dashboards** 

## About PPRD Systems

PPRD Systems is an engineering startup specializing in **IoT, Healthcare, and Fintech software solutions**, combining **real-time data pipelines**, **AI integration**, and **cloud-native architecture**.

We focus on:
- Scalable data processing with Kafka, Flink, and Pekko.
- Predictive analytics and intelligent automation.
- Modular, event-driven system design.

**Website:** Coming Soon  
**LinkedIn:** [PPRD Systems](https://www.linkedin.com/company/pprdsystems)  
**Contact:** contact@pprdsystems.com

## License
This project is licensed under the [Apache License 2.0](LICENSE).


> # ⚠️ Disclaimer
> This repository is part of an ongoing **research and experimental development projects** conducted by **PPRD Systems, LLC** for educational and exploratory purposes only.
>  
> ⚖️ **Liability & Usage**
Use of this repository or its components is **strictly at the user’s own risk**.
**PPRD Systems, LLC** provides all materials **“as is,” without any warranties**, express or implied.
> 
> © **2025 PPRD Systems, LLC**. All rights reserved