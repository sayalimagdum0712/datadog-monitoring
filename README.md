# 📊 Real-time Infrastructure Monitoring with Datadog

![Datadog](https://img.shields.io/badge/Datadog-Agent_7.80.3-purple?logo=datadog)
![AWS](https://img.shields.io/badge/AWS-EC2-orange?logo=amazonaws)
![Ubuntu](https://img.shields.io/badge/Ubuntu-26.04_LTS-purple?logo=ubuntu)
![Docker](https://img.shields.io/badge/Docker-29.1.3-blue?logo=docker)
![Status](https://img.shields.io/badge/Monitoring-Active-brightgreen)

---

## 📌 Project Overview

Installed and configured **Datadog Agent 7.80.3** on AWS EC2 for real-time infrastructure monitoring and observability. Monitors CPU, Memory, Disk, Uptime, and Docker containers with live dashboards on Datadog platform.

> 📈 **Live Metrics: CPU 0.35% | Memory 84.31% | Uptime 4.99hrs | 3 Docker Containers**

---

## 🏗️ Architecture Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                  AWS EC2 Instance                             │
│            (Ubuntu 26.04 LTS - m7i-flex.large)               │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │              Docker Engine 29.1.3                      │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐            │  │
│  │  │  Nginx   │  │  Tomcat  │  │  MySQL   │            │  │
│  │  │  :80     │  │  :8080   │  │  :3306   │            │  │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘            │  │
│  └────────┼────────────┼────────────┼───────────────────┘  │
│           │            │            │                        │
│  ┌────────▼────────────▼────────────▼───────────────────┐  │
│  │              Datadog Agent 7.80.3                     │  │
│  │                                                       │  │
│  │  Collects every 15 seconds:                          │  │
│  │  ├── System Metrics (CPU, RAM, Disk, Network)        │  │
│  │  ├── Docker Container Metrics                        │  │
│  │  ├── Process Information                             │  │
│  │  └── System Logs                                     │  │
│  └───────────────────────┬───────────────────────────────┘  │
└──────────────────────────┼───────────────────────────────────┘
                           │ HTTPS (secure)
                           │ Sends metrics every 15s
                           ▼
┌──────────────────────────────────────────────────────────────┐
│              Datadog Cloud Platform                           │
│               (app.datadoghq.com)                            │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐  │
│  │  Host Metrics│  │Infrastructure│  │    Dashboards    │  │
│  │  Dashboard   │  │    List      │  │   & Alerts       │  │
│  └──────────────┘  └──────────────┘  └──────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```
datadog-monitoring/
│
├── datadog-config.yaml         # Datadog agent configuration
├── .gitignore                  # Excludes sensitive files
├── screenshots/
│   ├── agent-running.png       # systemctl status datadog-agent
│   ├── host-metrics.png        # Datadog Host Metrics dashboard
│   ├── infrastructure-list.png # Datadog Infrastructure List
│   └── cpu-memory-graphs.png   # Real-time metric graphs
└── README.md                   # This file
```

---

## 🛠️ Tech Stack

| Tool | Version | Purpose |
|------|---------|---------|
| Datadog Agent | 7.80.3 | Metrics collection & monitoring |
| AWS EC2 | m7i-flex.large | Cloud server being monitored |
| Ubuntu | 26.04 LTS | Server operating system |
| Docker | 29.1.3 | Container runtime (monitored) |
| systemd | Latest | Agent service management |

---

## 📈 Metrics Captured

| Metric | Value | Dashboard Location |
|--------|-------|-------------------|
| Memory Usage (%) | 84.31% | Host Metrics → Overview |
| CPU Usage (%) | 0.35% | Host Metrics → CPU |
| CPU Idle (%) | 99.48% | Host Metrics → CPU |
| CPU iowait (%) | 0.015% | Host Metrics → CPU |
| System Uptime | 4.99 hrs | Host Metrics → Overview |
| Agent NTP Offset | 0.000s | Host Metrics → Overview |
| Docker Containers | 3 running | Container Map |

---

## 📄 Source Code

### datadog-config.yaml

```yaml
# ==========================================
# Datadog Agent Configuration
# AWS EC2 Ubuntu 26.04 LTS
# ==========================================

# Datadog site (US1)
site: datadoghq.com

# Custom hostname for easy identification
hostname: aws-ec2-sayali

# Environment tags for organization
tags:
  - env:production
  - project:devops
  - cloud:aws
  - owner:sayali
  - instance:m7i-flex.large

# Enable log collection
logs_enabled: true

# Process monitoring
process_config:
  enabled: true

# Docker container monitoring
docker_labels_as_tags:
  com.docker.compose.service: service_name

# Collection interval (seconds)
min_collection_interval: 15
```

### .gitignore

```bash
# Never push these to GitHub!
*.key           # API keys
*.token         # Access tokens
auth_token      # Datadog auth token
*.secret        # Secret files
datadog.yaml    # Contains actual API key
```

---

## 🚀 Setup Steps

### Step 1: Create Datadog Account

```
1. Go to: datadoghq.com
2. Click "Get Started Free"
3. Sign up with your email
4. Select region: US1 (datadoghq.com)
5. Answer setup questions:
   → What to monitor: Infrastructure & backend applications
   → How to monitor: Install the Datadog Agent
   → Platform: Linux
```

### Step 2: Get Your API Key

```
1. Login to app.datadoghq.com
2. Bottom left → Click your name
3. Click "Organization Settings"
4. Click "API Keys"
5. Click "New Key"
6. Name: AWS-EC2-Key
7. Click "Create Key"
8. COPY THE KEY IMMEDIATELY!
   (It is shown only once!)
```

### Step 3: Install Datadog Agent on EC2

```bash
# Copy this command from Datadog website
# Replace YOUR_API_KEY with actual key
DD_API_KEY=YOUR_API_KEY \
DD_SITE="datadoghq.com" \
bash -c "$(curl -L https://install.datadoghq.com/scripts/install_script_agent7.sh)"

# This will install:
# - datadog-agent 7.80.3
# - datadog-signing-keys
# Total: ~159 MB download
```

### Step 4: Verify Installation

```bash
sudo systemctl status datadog-agent
```

Expected Output:
```
● datadog-agent.service - Datadog Agent
     Loaded: loaded (/usr/lib/systemd/system/datadog-agent.service)
     Active: active (running) ✅
```

### Step 5: Fix Configuration (if API key missing)

```bash
# Create configuration file
sudo sh -c 'cat > /etc/datadog-agent/datadog.yaml << EOF
api_key: YOUR_ACTUAL_API_KEY
site: datadoghq.com
EOF'
```

### Step 6: Fix File Permissions

```bash
sudo chown -R dd-agent:dd-agent /etc/datadog-agent
sudo chown -R dd-agent:dd-agent /opt/datadog-agent
```

### Step 7: Start Agent

```bash
# Start the agent
sudo systemctl start datadog-agent

# Enable auto-start on reboot
sudo systemctl enable datadog-agent

# Check status
sudo systemctl status datadog-agent
```

Expected Output:
```
Active: active (running) since Sat 2026-06-27 17:19:24 UTC ✅
Main PID: 19794 (agent)
Memory: 31.3M
```

### Step 8: Verify Data is Sending

```bash
sudo datadog-agent status 2>/dev/null | grep -i "forwarder\|connected\|api"
```

Expected Output:
```
Forwarder
    On-disk storage is disabled.
API Keys status: OK ✅
```

### Step 9: View Dashboard on Datadog

```
1. Go to app.datadoghq.com
2. Left menu → Infrastructure
3. Click "Host List"
4. See your EC2: i-00f7b64e691c0cf08 ✅
5. Click on host for details
6. Go to Dashboards → Host Metrics
7. See beautiful real-time graphs! 📊
```

### Step 10: Push Config to GitHub

```bash
mkdir ~/datadog-monitoring && cd ~/datadog-monitoring

# Create files (already done above)
git init
git add .
git commit -m "Added Datadog monitoring configuration for AWS EC2"
git branch -M main
git remote add origin https://YOUR_TOKEN@github.com/sayalimagdum0712/datadog-monitoring.git
git push -u origin main
```

---

## 🔧 Useful Commands

```bash
# Check agent status
sudo systemctl status datadog-agent

# Start agent
sudo systemctl start datadog-agent

# Stop agent
sudo systemctl stop datadog-agent

# Restart agent
sudo systemctl restart datadog-agent

# Enable auto-start
sudo systemctl enable datadog-agent

# View detailed agent status
sudo datadog-agent status

# Check agent version
sudo datadog-agent version
# Expected: Agent 7.80.3

# View agent logs
sudo journalctl -u datadog-agent -f

# Test connectivity to Datadog
sudo datadog-agent status 2>/dev/null | grep -i "api\|forwarder"

# Check config file
sudo cat /etc/datadog-agent/datadog.yaml
```

---

## 📊 Dashboard Views Available

| Dashboard | Path | Shows |
|-----------|------|-------|
| Host Metrics | Dashboards → Host Metrics | CPU, Memory, Disk, Uptime |
| Infrastructure List | Infrastructure → Host List | All monitored hosts |
| Container Map | Infrastructure → Container Map | Docker containers |
| Process List | Infrastructure → Processes | Running processes |
| Log Management | Logs → Explorer | System & app logs |
| Network | Infrastructure → Network | Network traffic |

---

## 📸 Screenshots

> Add screenshots in `screenshots/` folder:
> - `screenshots/agent-running.png` → `systemctl status` output
> - `screenshots/host-metrics.png` → Datadog Host Metrics dashboard
> - `screenshots/infrastructure-list.png` → Host appearing in list
> - `screenshots/cpu-memory.png` → Real-time metric graphs

---

## ⚠️ Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| Agent inactive | Not started | `sudo systemctl start datadog-agent` |
| API Key invalid | Wrong key in config | Update `/etc/datadog-agent/datadog.yaml` |
| Auth token error | Missing token file | `sudo rm -f /etc/datadog-agent/auth_token` then restart |
| Permission denied | Wrong file owner | `sudo chown -R dd-agent:dd-agent /etc/datadog-agent` |
| No hosts showing | First data delay | Wait 5-10 minutes and refresh |
| GCE metadata error | Not a GCE instance | Normal warning on AWS, ignore it |

---

## 💡 Monitoring Concepts Explained

```
Observability = Metrics + Logs + Traces

Metrics:
→ Numerical data over time
→ CPU %, Memory %, Disk usage
→ What is happening?

Logs:
→ Text records of events
→ Error messages, access logs
→ Why did it happen?

Traces:
→ Request flow through system
→ Performance bottlenecks
→ Where is it slow?

Datadog Agent:
→ Collects all three (Metrics + Logs + Traces)
→ Sends to Datadog platform
→ Visualizes on dashboards
→ Alerts when something goes wrong
```

---

## 🎯 Key Learnings

- Datadog Agent installation and configuration on Linux
- Real-time infrastructure monitoring concepts
- CPU, Memory, Disk metric interpretation
- Docker container monitoring with Datadog
- Creating and reading monitoring dashboards
- systemd service management (start/stop/enable)
- Observability concepts (Metrics, Logs, Traces)
- Secure API key management

---

## 👩‍💻 Author

**Sayali Magdum**
- 🔗 GitHub: [@sayalimagdum0712](https://github.com/sayalimagdum0712)
- 💼 LinkedIn: [Sayali Magdum](https://linkedin.com/in/sayalimagdum)
- 🎯 Role: Aspiring Cloud & DevOps Engineer

---

## 📅 Project Date
June 29, 2026
