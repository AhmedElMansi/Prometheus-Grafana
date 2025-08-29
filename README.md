# Prometheus + Grafana Monitoring Setup

This project demonstrates a simple monitoring setup using **Prometheus** and **Grafana** for two Linux virtual machines (Ubuntu and Rocky Linux) using **Node Exporter**.

---

## Project Overview

- **Prometheus**: Scrapes metrics from Node Exporters on two VMs and itself.  
- **Grafana**: Visualizes metrics from Prometheus with dashboards.  
- **Node Exporter**: Collects system metrics (CPU, memory, disk, network) from each VM.  
- **Docker Compose**: Runs Prometheus and Grafana containers for easy deployment.

---

## Prerequisites

- Docker & Docker Compose installed on host machine  
- Two Linux VMs (Ubuntu + Rocky Linux)  
---

## Problems Faced & Solutions

1. **Problem:** The host machine could not reach the VMs initially.  
   **Solution:** Added an additional **Host-Only Network adapter** to both VMs in VirtualBox, in addition to the NAT network. This allowed the host to communicate with the VMs.

2. **Problem:** Unable to access Node Exporter on the Rocky Linux VM via `curl` (connection refused).  
   **Solution:** Opened firewall port 9100 on the Rocky VM:

   ```
   sudo firewall-cmd --add-port=9100/tcp --permanent
   sudo firewall-cmd --reload
   ```
---
## VM Node Exporter Setup
1. Download Node Exporter
```
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
tar xvf node_exporter-1.8.1.linux-amd64.tar.gz
cd node_exporter-1.8.1.linux-amd64
```
2. Run Node Exporter
```
./node_exporter &
```
3. Test
```
curl http://<VM_HostOnlyInterface_IP>:9100/metrics
```
Expected Output

<img width="866" height="248" alt="image" src="https://github.com/user-attachments/assets/9df8486b-e24a-4bcc-9ed4-ba8468ecbc19" />

---

## Prometheus Setup
1. Edit `prometheus.yml` to add your targets
2. Start Prometheus + Grafana using docker
```
docker compose up -d
```

## Grafana Setup
1. Login using default username and password `admin:admin`
2. Add Prometheus data source
   - Go to **Connections → Add new connection → Prometheus**
   - Since Grafana runs inside the Docker Compose network, use the service name `prometheus` instead of an IP address.
     - Connection URL -> http://prometheus:9090
  <img width="2299" height="650" alt="image" src="https://github.com/user-attachments/assets/d31e6a5b-32c1-42b9-9911-248e8091d030" />

3. You can either create a custom dashboard or import the Node Exporter full dashboard (ID 1860) for a ready-made view of CPU, memory, disk, and network metrics.

<img width="2557" height="798" alt="image" src="https://github.com/user-attachments/assets/7d886ec6-6278-47c5-bf97-8e267066072b" />
