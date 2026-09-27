```
                          [ User / Browser ]
                                  │
                                  │ (Port Forwarding via kubectl)
                                  ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│ MINIKUBE CLUSTER                                                                │
│                                                                                 │
│  ┌───────────────────────┐                                                      │
│  │  Prometheus Operator  │                                                      │
│  └───────────┬───────────┘                                                      │
│              │ 1. Watches for ServiceMonitors                                   │
│              │    matching `release: prometheus`                                │
│              ▼                                                                  │
│  ┌───────────────────────┐ 2. Discovers target IP & port ┌───────────────────┐  │
│  │   Prometheus Pod      ├──────────────────────────────>│ MongoDB Exporter  │  │
│  │   (Port 9090)         │<──────────────────────────────┤ Pod (Port 9216)   │  │
│  └───────────┬───────────┘ 3. Scrapes /metrics endpoint  └─────────┬─────────┘  │
│              │                                                     │            │
│              │ Exposes metrics                                     │ Connects   │
│              ▼                                                     ▼            │
│  ┌───────────────────────┐                               ┌───────────────────┐  │
│  │      Grafana UI       │                               │ MongoDB Database  │  │
│  │   (Port 3000)         │                               │   (Port 27017)    │  │
│  └───────────────────────┘                               └───────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────────┘

```

---

### End-to-End Architectural Flow

1. **Deployment & Registration (`ServiceMonitor`)**
When you install the MongoDB Exporter Helm chart with `--set serviceMonitor.enabled=true` and `--set serviceMonitor.additionalLabels.release=prometheus`, Helm creates a `ServiceMonitor` Custom Resource with the label `release: prometheus`.
2. **Target Discovery (`Prometheus Operator`)**
* The **Prometheus Operator** continuously watches the Kubernetes API.
* It finds the `ServiceMonitor` because its `release: prometheus` label matches the `serviceMonitorSelector` inside the Prometheus configuration.


3. **Endpoint Resolution (`Kubernetes API`)**
* Prometheus reads the `spec.selector` in the `ServiceMonitor` to locate the `mongodb-exporter` Kubernetes Service.
* It queries the Kubernetes `Endpoints` API to extract the real-time Pod IP address (e.g., `10.244.0.15`) and container port (`9216`).


4. **Metrics Scraping (`Prometheus <-> Exporter`)**
* Every scrape interval (e.g., 30s), Prometheus sends an HTTP `GET` request directly to `http://<POD_IP>:9216/metrics`.
* The **MongoDB Exporter** translates queries to the MongoDB database into Prometheus metric formats and returns them.


5. **Visualization & Management**
* **Grafana** queries Prometheus to display dashboard graphs.
* **Alertmanager** receives alerts from Prometheus if thresholds are breached.
* **`kubectl port-forward`** exposes these internal cluster ports (`9090`, `3000`, `9093`, `9216`) to your local machine so you can view them in a web browser.