# 📊 Windows Monitoring Stack with Prometheus & Grafana (Docker)

A complete local monitoring solution for Windows using **Prometheus**, **Grafana**, **Windows Exporter**, and **Docker Desktop**.

This project demonstrates how to collect, store, and visualize real-time system metrics such as CPU usage, memory utilization, disk activity, and running processes from a Windows machine without requiring any cloud infrastructure.

---

# 🚀 Project Overview

The goal of this project is to build a monitoring platform capable of observing the health and performance of a Windows system in real time.

The monitoring stack consists of:

* **Windows Exporter** → Collects Windows system metrics
* **Prometheus** → Scrapes and stores metrics
* **Grafana** → Visualizes metrics through dashboards
* **Docker Desktop** → Hosts Prometheus and Grafana containers

Unlike cloud-based monitoring architectures that require multiple virtual machines, VPCs, subnets, and firewall rules, this setup runs entirely on a single Windows machine.

---

# 🏗️ Architecture

```text
┌─────────────────────────────┐
│      Windows Machine        │
│                             │
│  Windows Exporter           │
│  Port: 9182                │
│                             │
└─────────────┬───────────────┘
              │
              │ Metrics
              ▼
┌─────────────────────────────┐
│      Prometheus Container   │
│         Port 9090           │
│                             │
│ Scrapes Windows Exporter    │
└─────────────┬───────────────┘
              │
              │ Queries
              ▼
┌─────────────────────────────┐
│       Grafana Container     │
│         Port 3000           │
│                             │
│ Visual Dashboards           │
└─────────────────────────────┘
```

---

# 🛠 Technologies Used

| Technology        | Purpose                             |
| ----------------- | ----------------------------------- |
| Docker Desktop    | Container platform                  |
| Windows Exporter  | Windows metrics collector           |
| Prometheus        | Monitoring and time-series database |
| Grafana           | Visualization and dashboards        |
| PowerShell        | Load testing                        |
| Docker Networking | Container communication             |

---

# 📋 Prerequisites

Before starting, install:

* Docker Desktop
* Windows Exporter
* Windows 10/11
* Internet connection for downloading Docker images

Recommended:

* WSL2 enabled
* Administrative privileges

---

# 📁 Project Structure

```text
C:\monitoring
│
├── prometheus.yml
│
└── (optional test files)
```

---

# Step 1: Install Docker Desktop

Download and install Docker Desktop.

Requirements:

* Enable WSL2 Backend
* Restart the machine after installation

Verify Docker:

```powershell
docker --version
```

Check running containers:

```powershell
docker ps
```

---

# Step 2: Install Windows Exporter

Download the MSI installer from:

https://github.com/prometheus-community/windows_exporter/releases

Install using default settings.

Verify service status:

```powershell
sc query windows_exporter
```

Expected result:

```text
STATE : RUNNING
```

Verify metrics endpoint:

```text
http://localhost:9182/metrics
```

You should see metrics similar to:

```text
windows_cpu_time_total
windows_memory_available_bytes
```

---

# Step 3: Create Monitoring Directory

```powershell
mkdir C:\monitoring
cd C:\monitoring
```

---

# Step 4: Create Prometheus Configuration

Create:

```text
C:\monitoring\prometheus.yml
```

Contents:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'windows-local'
    static_configs:
      - targets:
          - 'host.docker.internal:9182'
```

### Why host.docker.internal?

Prometheus runs inside a Docker container.

Inside the container:

```text
localhost = container itself
```

To access services running on Windows host:

```text
host.docker.internal
```

must be used.

---

# Step 5: Create Docker Network

Create a custom network:

```powershell
docker network create monitoring-net
```

Why?

* Enables container-to-container communication
* Provides automatic DNS resolution
* Allows Grafana to locate Prometheus by container name

---

# Step 6: Run Prometheus

```powershell
docker run -d ^
--name prometheus ^
--network monitoring-net ^
-p 9090:9090 ^
-v C:\monitoring\prometheus.yml:/etc/prometheus/prometheus.yml ^
prom/prometheus
```

Verify:

```powershell
docker ps
```

Access:

```text
http://localhost:9090
```

---

# Step 7: Run Grafana

```powershell
docker run -d ^
--name grafana ^
--network monitoring-net ^
-p 3000:3000 ^
grafana/grafana
```

Verify:

```powershell
docker ps
```

Access:

```text
http://localhost:3000
```

Default credentials:

```text
Username: admin
Password: admin
```

You will be prompted to create a new password on first login.

---

# Step 8: Verify Prometheus Scraping

Open:

```text
http://localhost:9090/targets
```

Expected:

```text
windows-local → UP
```

If the target is UP, Prometheus is successfully scraping Windows Exporter.

---

# Step 9: Connect Grafana to Prometheus

Navigate to:

```text
Connections → Data Sources → Add Data Source
```

Choose:

```text
Prometheus
```

Set URL:

```text
http://prometheus:9090
```

Click:

```text
Save & Test
```

Expected:

```text
Data source is working
```

---

# Step 10: Import Dashboard

Recommended Dashboard ID:

```text
23942
```

Older Alternative:

```text
10467
```

Import:

```text
Dashboards → Import
```

Enter:

```text
23942
```

Select:

```text
Prometheus Data Source
```

Click:

```text
Import
```

---

# 📈 Metrics Monitored

The dashboard provides:

### CPU Metrics

* CPU Utilization
* CPU Time
* CPU Core Activity

### Memory Metrics

* Memory Usage
* Available Memory
* Memory Pressure

### Disk Metrics

* Read Operations
* Write Operations
* Disk Throughput

### System Metrics

* Running Processes
* System Uptime
* Service Statistics

---

# 🔥 Load Testing

## CPU Load Test

Generate CPU activity:

```powershell
1..2 | ForEach-Object {
    Start-Job { while ($true) { } }
}
```

Stop:

```powershell
Get-Job | Stop-Job
Get-Job | Remove-Job
```

---

## Disk Load Test

Create a 100MB file:

```powershell
fsutil file createnew C:\monitoring\testfile.dat 104857600
```

Delete file:

```powershell
Remove-Item C:\monitoring\testfile.dat
```

Observe Grafana dashboard updates.

---

# ⚠ Common Issues and Fixes

## Issue 1: Grafana Cannot Find Prometheus

Error:

```text
lookup prometheus: no such host
```

Cause:

Containers are not on the same Docker network.

Fix:

```powershell
docker network create monitoring-net
```

Restart both containers using:

```powershell
--network monitoring-net
```

---

## Issue 2: Dashboard ID Not Found

Error:

```text
Could not find a valid Grafana.com ID
```

Cause:

Invalid dashboard ID.

Fix:

Use:

```text
23942
```

---

## Issue 3: Dashboard Labels in Chinese

Cause:

Dashboard 10467 was created by a Chinese author.

Fix:

Import:

```text
23942
```

instead.

---

# 📊 Benefits of This Setup

✅ No cloud infrastructure required

✅ No VPC or subnet configuration

✅ No firewall rule management

✅ Fully containerized monitoring stack

✅ Real-time system visibility

✅ Easy deployment and maintenance

✅ Industry-standard monitoring tools

---

# 🔄 Restarting After Reboot

Windows Exporter starts automatically as a Windows Service.

Prometheus and Grafana containers can be started manually:

```powershell
docker start prometheus
docker start grafana
```

Or configure automatic startup:

```powershell
--restart unless-stopped
```

when creating the containers.

---

# 🎯 Learning Outcomes

By completing this project you gain experience with:

* Infrastructure Monitoring
* Observability Concepts
* Docker Networking
* Prometheus Configuration
* Grafana Dashboards
* Windows Performance Monitoring
* Time-Series Databases
* Containerized Services

These skills are directly applicable to DevOps, Cloud Engineering, SRE, and Platform Engineering roles.

---

# 📚 References

* Prometheus Documentation
* Grafana Documentation
* Windows Exporter Documentation
* Docker Desktop Documentation

---

## 👨‍💻 Author

Windows Monitoring Stack using Prometheus, Grafana, Docker, and Windows Exporter for real-time system observability and performance monitoring.
