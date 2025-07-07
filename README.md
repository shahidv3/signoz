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
   ```yaml
   receivers:
     hostmetrics:
       collection_interval: 60s
       scrapers:
         cpu:
         memory:
         disk:
         network:

   exporters:
     otlp:
       endpoint: <SIGNOZ_OTEL_COLLECTOR_IP>:4317
       tls:
         insecure: true

   service:
     pipelines:
       metrics:
         receivers: [hostmetrics]
         exporters: [otlp]
   ```
3. Run with:
   ```bash
   otelcol.exe --config otelcol-windows-config.yaml
   ```

---

## 🐧 Linux VM Monitoring
```yaml
receivers:
  hostmetrics:
    collection_interval: 60s
    scrapers:
      cpu:
      memory:
      disk:
      filesystem:
      network:

exporters:
  otlp:
    endpoint: <SIGNOZ_OTEL_COLLECTOR_IP>:4317
    tls:
      insecure: true

service:
  pipelines:
    metrics:
      receivers: [hostmetrics]
      exporters: [otlp]
```
Run with:
```bash
otelcol --config otelcol-linux-config.yaml
```

---

## 🗄️ PostgreSQL Monitoring (Exporter)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres-exporter
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres-exporter
  template:
    metadata:
      labels:
        app: postgres-exporter
    spec:
      containers:
        - name: exporter
          image: prometheuscommunity/postgres-exporter
          ports:
            - containerPort: 9187
          env:
            - name: DATA_SOURCE_NAME
              value: "postgresql://user:password@postgresql-host:5432/dbname?sslmode=disable"
```

---

## 📊 Access Dashboards
```bash
kubectl port-forward svc/signoz-frontend 3301:3301
```
Then visit [http://localhost:3301](http://localhost:3301)

---

## 📬 Contact
For questions, raise an issue or reach out to your observability team.
