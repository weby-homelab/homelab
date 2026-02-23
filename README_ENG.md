# 🌌 WEBy Home Lab: Infrastructure Matrix

<p align="center">
  <img src="https://img.shields.io/badge/Infrastructure-as--Code-blueviolet?style=for-the-badge&logo=ansible" alt="IaC">
  <img src="https://img.shields.io/badge/Security-Zero--Trust-red?style=for-the-badge&logo=cloudflare" alt="Security">
  <img src="https://img.shields.io/badge/Status-Active-success?style=for-the-badge" alt="Status">
  <img src="https://img.shields.io/badge/2026-Ready-yellow?style=for-the-badge" alt="Year">
</p>

Welcome to the central hub of **WEBy Home Lab** — an automated, secure, and resilient infrastructure ecosystem that bridges cloud resources and local clusters into a single living organism.

This repository stores the intelligence of my lab: from security configurations to critical monitoring systems for Kyiv.

---

## 🏗 Ecosystem Architecture

Our infrastructure is built on **Hybrid Cloud** and **Zero Trust** principles. All nodes are interconnected via **Tailscale Mesh VPN** and secured through **Cloudflare Tunnels**.

```mermaid
graph TD
    %% -- Styles --
    classDef cloud fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    classDef local fill:#e3f2fd,stroke:#1565c0,stroke-width:2px
    classDef engine fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px
    classDef notify fill:#fffde7,stroke:#fbc02d,stroke-width:2px

    %% -- Nodes --
    subgraph Cloud ["☁️ Cloud Edge (Hetzner / IONOS)"]
        direction TB
        FLASH["⚡ Flash Monitor (Unified)"]
        VOIP["📞 VoIP Core (Asterisk 22)"]
        KUMA["📊 Uptime Kuma"]
    end

    subgraph Local ["🏠 Home Core (Proxmox Cluster)"]
        direction TB
        PVE["🖥️ PVE Node (LXC/VM)"]
        ADG["🛡️ AdGuard Home"]
        NAS["🗄️ Storage Cluster"]
    end

    subgraph User ["📱 Access Layer"]
        PWA["Web Dashboard (PWA)"]
        TG["Telegram Bot API"]
    end

    %% -- Flows --
    Local <==>|Tailscale Mesh| Cloud
    Cloud <-->|Cloudflare Tunnel| PWA
    FLASH -->|Intelligence| TG
    Local -->|Stats| KUMA
    VOIP -.->|Secure SIP| User

    %% -- Apply Styles --
    class Cloud cloud
    class Local local
    class FLASH,VOIP,KUMA engine
    class PWA,TG notify
```

---

## 🚀 Core Projects

The ecosystem consists of several independent yet integrated modules:

### ⚡ [Flash Monitor Kyiv](https://github.com/weby-homelab/flash-monitor-kyiv) (Flagship)
**Unified security and power monitoring system.**
- **Status:** 🟢 v1.2 Stable
- **Overview:** Fully autonomous monitoring of power, air raid alerts, and AQI.
- **Key Feature:** Local Yasno/DTEK parsing and "Plan vs Fact" analytics.

### 📊 [Light Monitor Kyiv](https://github.com/weby-homelab/light-monitor-kyiv)
**Deep power grid analytics.**
- **Overview:** Focuses on statistical reports and schedule compliance accuracy.

### 🛡️ [Security Monitor Kyiv](https://github.com/weby-homelab/security-monitor-kyiv)
- **Overview:** Specialized dashboard for wall displays: alerts, radiation, environment.

### 📞 [VoIP Installer](https://github.com/weby-homelab/voip-installer)
- **Overview:** Automated deployment of secure Asterisk 22 telephony in Docker.

---

## 🖥️ Hardware Stack

| Node | Location | Role | OS / Hypervisor |
| :--- | :--- | :--- | :--- |
| **HTZNR (Primary)** | Germany | Edge Services, Flash Monitor | Ubuntu 24.04 LTS |
| **IONOS-VPS** | Europe | Backup VoIP, DNS, Turnserver | Debian (Tmux Hardened) |
| **PRXMX-02** | Home Lab | Central Core, NAS, AdGuard | Proxmox VE 9.1 |
| **PRXMX-01** | Home Lab | Backup Node (Battery Monitored) | Proxmox VE (Laptop) |

---

## 🗺️ 2026 Roadmap

- [ ] **Infrastructure as Code:** Full migration to Ansible playbooks for all nodes.
- [ ] **Secret Management:** Implementing HashiCorp Vault for token security.
- [ ] **Observability:** Prometheus + Grafana stack for hardware visualization.
- [ ] **AI Integration:** Implementing Gemini API for intelligent log analysis.

---
<p align="center">
  ✦ 2026 WEBy Home Lab ✦<br>
  <i>"Automate everything you do twice. Monitor everything that matters."</i>
</p>
