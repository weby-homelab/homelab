<p align="center">
  <a href="README_ENG.md">
    <img src="https://img.shields.io/badge/🇬🇧_English-00D4FF?style=for-the-badge&logo=readme&logoColor=white" alt="English README">
  </a>
  <a href="README.md">
    <img src="https://img.shields.io/badge/🇺🇦_Українська-FF4D00?style=for-the-badge&logo=readme&logoColor=white" alt="Українська версія">
  </a>
</p>

<br>

# 🌌 Weby Homelab: Infrastructure Matrix

<p align="center">
  <img src="https://img.shields.io/badge/Infrastructure-as--Code-blueviolet?style=for-the-badge&logo=ansible" alt="IaC">
  <img src="https://img.shields.io/badge/Security-Zero--Trust-red?style=for-the-badge&logo=cloudflare" alt="Security">
  <img src="https://img.shields.io/badge/Status-Active-success?style=for-the-badge" alt="Status">
  <img src="https://img.shields.io/badge/Updated-April%202026-yellow?style=for-the-badge" alt="Updated">
</p>

Welcome to the central hub of **Weby Homelab** — an automated, secure, and resilient infrastructure ecosystem that bridges cloud resources and local clusters into a single living organism.

This repository stores the intelligence of my lab: from security configurations and traffic monitoring to critical outage and air raid alert systems for Kyiv.

---

## 🏗 Ecosystem Architecture (Mega-Topology)

Our infrastructure is deployed based on **Hybrid Cloud**, **Zero Trust**, and **Secure by Design** principles. All nodes are interconnected via **Tailscale Mesh VPN**, governed by centralized rules (Niftywall / Firewalld), and secured through **Cloudflare Tunnels**.

```mermaid
graph TD
    %% -- Styles --
    classDef cloud fill:#2d3436,stroke:#7b1fa2,stroke-width:2px,color:#fff
    classDef local fill:#2d3436,stroke:#1565c0,stroke-width:2px,color:#fff
    classDef service fill:#0984e3,stroke:#74b9ff,stroke-width:2px,color:#fff
    classDef security fill:#d63031,stroke:#ff7675,stroke-width:2px,color:#fff
    classDef network fill:#00b894,stroke:#81ecec,stroke-width:2px,color:#fff
    classDef external fill:#f39c12,stroke:#ffeaa7,stroke-width:2px,color:#000

    %% -- Core Networks --
    TS(("🌐 Tailscale Mesh VPN")):::network
    CF(("🛡️ Cloudflare Tunnels & WAF")):::security

    %% -- Node: HTZNR (Primary) --
    subgraph HTZNR ["☁️ HTZNR (Primary Cloud Node - Ubuntu 24.04)"]
        direction TB
        FW["🔥 Firewalld-GUI (Docker)"]:::security
        NW["🛡️ Niftywall (Bare Metal)"]:::security
        FLASH["⚡ Flash Monitor (v3.2.1)"]:::service
        VOIP["📞 Asterisk VoIP (v4.6.0)"]:::service
        KUMA["📊 Uptime Kuma"]:::service
        ARC["🔮 Arcane"]:::service
        SSH["🚪 SSH Port Changer"]:::security
    end

    %% -- Node: PRXMX-02 (Home Core) --
    subgraph PRXMX ["🏠 PRXMX-02 (Proxmox Cluster Core)"]
        direction TB
        LXC["🐳 LXC-200 (Docker Testbed)"]:::service
        ADG["🛑 AdGuard Home (DNS)"]:::network
        NAS["🗄️ Storage Cluster"]:::service
        PING["📡 Outage Ping Scripts"]:::service
    end

    %% -- Node: Backup Clouds --
    subgraph Backup ["☁️ Backup Cloud Edge"]
        direction LR
        IONOS["🇪🇺 IONOS (Docker Test/Backup)"]:::cloud
        SRVRS["🌍 SRVRS-ONLINE (Backup)"]:::cloud
    end

    %% -- External Clients & APIs --
    subgraph Ext ["📱 External World"]
        direction TB
        TG["💬 Telegram API (Prod/Test)"]:::external
        WEB["🖥️ Web Admin PWA"]:::external
        SVITLO["💡 Svitlobot API"]:::external
    end

    %% -- Connections --
    HTZNR <==>|Encrypted Tunnel| TS
    PRXMX <==>|Encrypted Tunnel| TS
    Backup <==>|Encrypted Tunnel| TS
    
    FLASH -->|Alerts & Updates| TG
    PING -->|Status Push 30s| FLASH
    PING -->|Status Push| SVITLO
    
    FW -.->|Configures| HTZNR
    NW -.->|Nftables Rules| HTZNR
    
    CF -->|Secure HTTP/HTTPS Routing| FLASH
    CF -->|Secure UI Access| FW
    CF -->|Web Access| WEB
    
    VOIP -->|SIP| WEB
```

---

## 🚀 Core Projects (Updated: April 2026)

The ecosystem consists of several independent yet integrated modules that act as a cohesive unit:

### ⚡ [Flash Monitor Kyiv](https://github.com/weby-homelab/flash-monitor-kyiv) (Flagship)
**Unified autonomous security and power monitoring system.**
- **Status:** 🟢 **Active v3.2.1**
- **Overview:** Full integration of power monitoring, air raid alerts, and AQI. Includes a "Quiet Mode" and a "Safety Net" (35s push timeout logic).
- **Key Features:** PWA dashboard (admin.srvrs.top), async caching (no deadlocks), strict security standards (LFI remediated).

### 🔥 [Firewalld-GUI](https://github.com/weby-homelab/firewalld-gui) & [Niftywall](https://github.com/weby-homelab/niftywall)
**Network defense and Zero-Trust filtering systems.**
- **Status:** 🟢 **Active (v1.6.0 & v1.5.0)**
- **Overview:** Firewalld-GUI provides a visual web interface to manage zones and ports, while Niftywall handles low-level nftables enforcement and Fail2Ban analytics.
- **Security:** Secret management updated, Path Traversal attacks blocked, secure JWT token generation enforced.

### 📞 [VoIP Installer](https://github.com/weby-homelab/voip-installer)
- **Overview:** Automated deployment of secure Asterisk telephony in Docker (v4.6.x). Hardened via Fail2Ban (asterisk-pjsip).

### 🛡️ Archived Projects (Integrated)
- **Light Monitor Kyiv / Security Monitor Kyiv:** Functionality fully absorbed into Flash Monitor v3.2+.
- **UFW GUI:** Deprecated and replaced by Firewalld-GUI & Niftywall for better Docker compatibility.

---

## 🖥️ Hardware Stack (April 2026)

| Node | Location | Role | OS / Hypervisor |
| :--- | :--- | :--- | :--- |
| **HTZNR (Primary)** | Germany | Prod Edge (Flash, Niftywall, Arcane) | Ubuntu 24.04 LTS (Bare Metal) |
| **PRXMX-02-LXC200**| Home Lab (Kyiv)| Prod Pings, Docker Testbed, AdGuard| Proxmox VE 9.1 (100.124.218.39)|
| **IONOS** | Europe | Docker Test Node, Backup | Debian (194.164.198.173) |
| **SRVRS-ONLINE** | Europe | Secondary Backup | Ubuntu (91.107.214.59) |

---

## 🗺️ 2026 Roadmap (Updated)

- [x] **Zero-Trust Security:** Comprehensive code audit, elimination of hardcoded secrets, closure of LFI vulnerabilities.
- [x] **Smart Asynchronous Logic:** Implementation of async caching (FastAPI) to prevent deadlocks in Flash Monitor.
- [ ] **Infrastructure as Code (IaC):** Full transition to Ansible playbooks to ensure idempotency across all servers (HTZNR, PRXMX, IONOS).
- [ ] **High Availability (HA):** Setup a failover cluster between HTZNR and IONOS to ensure continuous Flash Monitor uptime if the primary datacenter fails.
- [ ] **AI-Driven Analytics:** Integrate Gemini / LLMs for automated analysis of Fail2Ban logs and Niftywall metrics (infrastructure self-healing).
- [ ] **IPv6 Rollout & Advanced WAF:** Complete IPv6 stack deployment and harden Cloudflare WAF rules for PWA dashboards.

---
<p align="center">
  ✦ 2026 Weby Homelab ✦ — infrastructure that doesn’t give up.<br>
  Made with ❤️ in Kyiv under air raid sirens and blackouts...
</p>
