# Elastic Stack on Windows 11 via VMware Fusion
### Running Elasticsearch & Kibana on a Mac M4 using VMware Fusion + Windows 11 ARM64

---

## Overview

This repo documents the setup of a local **Elasticsearch + Kibana** development environment on a **Mac M4 (Apple Silicon)** using **VMware Fusion** to run **Windows 11 ARM64** — built as job preparation for a Splunk → Elasticsearch migration project.

It includes:
- Step-by-step Windows 11 VM setup on Mac M4
- Elasticsearch & Kibana installation on Windows
- CLI comparison across macOS, Windows, and Linux
- Splunk → Elasticsearch concept mapping
- A Jupyter notebook walkthrough

---

## Environment

| Component | Details |
|-----------|---------|
| Host Machine | MacBook Air M4 (Apple Silicon) |
| Virtualisation | VMware Fusion 13 |
| Guest OS | Windows 11 Pro ARM64 (Build 26200) |
| Elasticsearch | 7.16.3 |
| Kibana | 7.16.3 |

---

## Repository Structure 

```
elastic-stack-windows/
│
├── README.md                        ← You are here
├── notebook/
│   └── elastic_stack_setup.ipynb    ← Step-by-step notebook walkthrough
├── configs/
│   ├── elasticsearch.yml            ← Sample ES config
│   └── kibana.yml                   ← Sample Kibana config
└── scripts/
    └── start-elastic.bat            ← One-click start script for Windows
```

---

## Prerequisites

- MacBook with Apple Silicon (M1/M2/M3/M4)
- VMware Fusion 13+ (free for personal use)
- Windows 11 ARM64 ISO — download from:
  > https://aka.ms/windowsinsiderpreview
  > Select: **Windows 11 Client ARM64 Insider Preview**

---

## Quick Start

### 1. Create Windows 11 VM in VMware Fusion
```
File → New → Install from disc or image
→ Select Win11_25H2_English_Arm64.iso
→ Firmware: UEFI + Secure Boot
→ RAM: 4096 MB minimum (6144 MB recommended)
→ CPU: 2-4 cores
→ Disk: 60 GB
→ Network: Share with my Mac (NAT)
```

### 2. Install Elasticsearch (Windows PowerShell)
```powershell
# Run PowerShell as Administrator
winget install Elastic.Elasticsearch

# Verify it's running
Get-Service elasticsearch*
curl http://localhost:9200
```

### 3. Install Kibana (Windows PowerShell)
```powershell
# Download ZIP manually (winget has cert issues with Kibana 7.x)
# https://www.elastic.co/downloads/past-releases/kibana-7-16-3
# → Select WINDOWS

# Extract to short path (important — avoids path-too-long error)
cd "C:\Users\YOUR_USERNAME\Downloads"
Expand-Archive -Path "kibana-7.16.3-windows-x86_64.zip" -DestinationPath "C:\kibana"

# Start Kibana
cd "C:\kibana\kibana-7.16.3-windows-x86_64\bin"
.\kibana.bat
```

### 4. Access Kibana UI
```
Open browser → http://localhost:5601
```

---

## Daily Start Commands

```powershell
# Start Elasticsearch (Windows service — auto starts)
net start elasticsearch

# Start Kibana
cd "C:\kibana\kibana-7.16.3-windows-x86_64\bin"
.\kibana.bat

# Verify Elasticsearch
curl http://localhost:9200/_cluster/health?pretty

# Open Kibana
start http://localhost:5601
```

---

## Known Issues & Fixes

**Boot loop into Windows Setup after VM restart**
```
Edit .vmx file on Mac:
bios.bootOrder = "hdd"    ← change from "CDROM"
Also disconnect ISO:
Virtual Machine → CD/DVD → Disconnect
```

**Kibana ZIP extraction fails (Path too long)**
```powershell
# Always extract to C:\kibana not inside Downloads
Expand-Archive -Path "kibana.zip" -DestinationPath "C:\kibana"
```

**Network not connected after VMware Tools install**
```powershell
netsh winsock reset
netsh int ip reset
ipconfig /renew
# Then restart Windows
```

**WSL2 not supported (nested virtualisation blocked on M4)**
```
Expected on Apple Silicon inside VMware Fusion
Workaround: use winget to install ES natively on Windows
```

---

## Notebook

See [`notebook/elastic_stack_setup.ipynb`](notebook/elastic_stack_setup.ipynb) for a full walkthrough including:
- VM configuration steps
- Installation commands
- API query examples
- KQL practice queries
- Splunk → KQL translation examples

---

## Related Resources

- [Elastic Official Docs](https://www.elastic.co/guide/index.html)
- [Splunk Migration Guide](https://www.elastic.co/guide/en/kibana/current/index.html)
- [VMware Fusion Download](https://www.vmware.com/products/fusion.html)
- [Windows 11 ARM64 ISO](https://aka.ms/windowsinsiderpreview)

---
