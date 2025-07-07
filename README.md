README.md

# SigNoz Observability POC

This repository sets up a full observability proof-of-concept using [SigNoz](https://signoz.io) and [OpenTelemetry](https://opentelemetry.io/) with support for:

- 🪟 Windows VM Monitoring
- 🐧 Linux VM Monitoring
- ☸️ Kubernetes Cluster Monitoring
- 🗄️ Database Monitoring (e.g., PostgreSQL)
- 📦 External ClickHouse for backend storage

---

## 📁 Repository Structure

```
.
├── charts
│   └── signoz-observability  # Helm chart with dependencies
├── windows
│   └── otelcol-windows-config.yaml  # Collector config for Windows VM
├── linux
│   └── otelcol-linux-config.yaml    # Collector config for Linux VM
├── db
│   └── postgres-exporter.yaml       # Kubernetes Deployment for DB metrics
└── README.md
```

---

## 🚀 Prerequisites
- Kubernetes cluster (GKE, OKE, or Minikube)
- Helm 3.x
- Optional: Windows & Linux VMs (for hostmetrics)

---

## 🛠️ Installation

### 1. Clone the Repo
```bash
git clone https://github.com/<your-org>/signoz-observability-poc.git
cd signoz-observability-poc
```

### 2. Install Helm Dependencies
```bash
cd charts/signoz-observability
helm dependency update
```

### 3. Deploy Stack
```bash
helm install signoz-poc . -f values.yaml
```

---

## 🪟 Windows VM Monitoring
1. Download [otelcol.exe](https://github.com/open-telemetry/opentelemetry-collector-releases/releases)
2. Use the config file:
   ```bash
   otelcol.exe --config windows/otelcol-windows-config.yaml
   ```
3. Ensure outbound access to your OTEL Collector service.

---

## 🐧 Linux VM Monitoring
```bash
otelcol --config linux/otelcol-linux-config.yaml
```

---

## 🗄️ Database Monitoring (e.g., PostgreSQL)
```bash
docker run -d -p 9104:9187 \
  -e DATA_SOURCE_NAME="postgresql://user:password@host:5432/dbname?sslmode=disable" \
  prometheuscommunity/postgres-exporter
```
> Ensure that the Prometheus scrape config in `values.yaml` includes this target.

---

## 📊 Access Dashboards
```bash
kubectl port-forward svc/signoz-frontend 3301:3301
```
Then visit [http://localhost:3301](http://localhost:3301)

---

## 📬 Contact
For questions, raise an issue or reach out to your observability team.
