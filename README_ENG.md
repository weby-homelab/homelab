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
  <img src="https://img.shields.io/badge/Updated-July%202026-purple?style=for-the-badge" alt="Updated">
</p>

Welcome to the central hub of **Weby Homelab** — an automated, secure, and resilient infrastructure ecosystem that bridges cloud resources and local clusters into a single living organism.

This repository stores the intelligence of my lab: from security configurations and traffic monitoring to critical outage and air raid alert systems for Kyiv.

---

## 🏗 Ecosystem Architecture (Mega-Topology)

Our infrastructure is deployed based on **Hybrid Cloud**, **Zero Trust**, and **Secure by Design** principles. All nodes are interconnected via **Tailscale Mesh VPN** (private IPs only, no public endpoints), governed by centralized Niftywall rules, and secured through **Cloudflare Tunnels**.

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

    %% -- Node: HTZNR (Cloud PROD) --
    subgraph HTZNR ["☁️ HTZNR (Cloud PROD — Hetzner, Tailscale: 100.64.0.1)"]
        direction TB
        PSA["⚡ Power-Safety-UA (v3.9.2)"]:::service
        NW["🛡️ Niftywall (v3.4.0)"]:::security
        VOIP["📞 Asterisk VoIP (v4.7.9)"]:::service
        KUMA["📊 Uptime Kuma"]:::service
        KARMA["🏆 Karma Community App"]:::service
        DOCK["🐳 Dockhand"]:::service
        WSRV["🌍 Weby-srvrs"]:::service
    end

    %% -- Node: PRXMX-01 (Home Core) --
    subgraph PRXMX01 ["🏠 PRXMX-01 (Home Core — Proxmox, Tailscale: 100.64.0.2)"]
        direction TB
        LXC200["🐳 LXC 200 (Docker, 100.64.0.3)"]:::service
        ADG["🛑 ADBlock-PD (DNS)"]:::network
        AQD["🌤️ Air Quality Dashboard"]:::service
        PING["📡 Outage Ping Scripts"]:::service
    end

    %% -- Node: PRXMX-02 (Home Backup) --
    subgraph PRXMX02 ["🏠 PRXMX-02 (Home Backup — Proxmox, Tailscale: 100.64.0.4)"]
        direction TB
        SAMBA["🗄️ Samba NAS"]:::service
        TRSN["📥 Transmission (LXC 400)"]:::service
        BKP["💾 Pull Backups (PRXMX-01 → HDD)"]:::service
    end

    %% -- External Clients & APIs --
    subgraph Ext ["📱 External World"]
        direction TB
        TG["💬 Telegram API"]:::external
        WEB["🖥️ Web Admin PWA"]:::external
    end

    %% -- Connections --
    HTZNR <==>|Encrypted| TS
    PRXMX01 <==>|Encrypted| TS
    PRXMX02 <==>|Encrypted| TS
    
    PSA -->|Alerts & Updates| TG
    PING -->|Status Push 30s| PSA
    
    NW -.->|Nftables Rules| HTZNR
    
    CF -->|Secure HTTP/HTTPS| PSA
    CF -->|Web Access| WEB
    CF -->|Secure UI| WSRV
    
    VOIP -->|SIP| WEB
```

---

## 🚀 Core Projects (Updated: July 2026)

The ecosystem consists of several independent yet integrated modules that act as a cohesive unit:

### ⚡ [Power-Safety-UA](https://github.com/weby-homelab/Power-Safety-UA) (Flagship)
**Unified autonomous security and power monitoring system.**
- **Status:** 🟢 **Active v3.9.2** (Docker Edition)
- **Overview:** Full integration of power monitoring, air raid alerts, AQI, and radiation monitoring. Includes "Quiet Mode" and "Safety Net" (35s push timeout logic).
- **Key Features:** PWA dashboard, Glassmorphism Admin Panel, async caching (no deadlocks), strict security standards (LFI remediated).
- **Evolution:** Successor to Flash Monitor Kyiv (v2.x) — fully rewritten on FastAPI + Docker.

### 🔥 [Firewalld-GUI](https://github.com/weby-homelab/firewalld-gui) & [Niftywall](https://github.com/weby-homelab/niftywall)
**Network defense and Zero-Trust filtering systems.**
- **Status:** 🟢 **Active (v1.6.13 & v3.4.0)**
- **Overview:** Firewalld-GUI provides a visual web interface to manage zones and ports, while Niftywall (rewritten in TypeScript) handles low-level nftables enforcement and Fail2Ban analytics.
- **Security:** Secret management updated, Path Traversal attacks blocked, secure JWT token generation enforced.

### 📞 [VoIP Installer](https://github.com/weby-homelab/voip-installer)
- **Overview:** Automated deployment of secure Asterisk telephony in Docker (v4.7.9). Hardened via Fail2Ban (asterisk-pjsip) and full TLS/SRTP stack.

### 🧠 [AI Second Brain GUI](https://github.com/weby-homelab/ai-second-brain-gui)
- **Overview:** Obsidian (Second Brain) web interface for accessing, searching and monitoring your knowledge base. Glassmorphism design on FastAPI.

### 📧 [Docker Mailserver GUI](https://github.com/weby-homelab/docker-mailserver-gui)
- **Overview:** Zero-trust mail server with Traefik proxy, SnappyMail GUI and full security stack.

### 🛡️ [ADBlock-PD](https://github.com/weby-homelab/ADBlock-PD)
- **Overview:** Hardcore hardened fork of AdGuard Home with zero telemetry. Completely severs all ties with AdGuard infrastructure.

### 🤖 [Safety Chat Bot](https://github.com/weby-homelab/safety-chat-bot)
- **Overview:** Telegram chat moderation bot with captcha, admin notifications and Aiogram 3.

### 🛡️ Archived Projects (Integrated)
- **Light Monitor Kyiv / Security Monitor Kyiv:** Functionality fully absorbed into Power-Safety-UA v3+.
- **UFW GUI:** Deprecated and replaced by Firewalld-GUI & Niftywall for better Docker compatibility.
- **Flash Monitor Kyiv:** Renamed and continued as Power-Safety-UA (FastAPI + Docker).
- **fm-ua (Flash-Monitor-UA v2.0):** A completely **different** project — P2P Energy Marketplace. NOT related to Flash Monitor Kyiv / Power-Safety-UA. Archived.
- **IONOS / SRVRS-ONLINE / PRXMX-03:** Decommissioned (2026). Infrastructure consolidated to HTZNR + PRXMX-01/02.

---

## 🖥️ Hardware Stack (July 2026)

| Node | Tailscale IP | Role | OS / Hypervisor |
| :--- | :--- | :--- | :--- |
| **HTZNR** | `100.64.0.1` | Cloud PROD (Power-Safety-UA, Niftywall, VoIP, Kuma) | Ubuntu 24.04 LTS (Bare Metal) |
| **PRXMX-01** | `100.64.0.2` | Home Core (LXC 200 Docker, ADBlock-PD, Ping Scripts) | Proxmox VE 9.2.3 |
| **LXC 200** | `100.64.0.3` | Docker Testbed (Power-Safety-UA staging, Air Quality) | Debian LXC on PRXMX-01 |
| **PRXMX-02** | `100.64.0.4` | Home Backup (Samba NAS, Transmission, Pull Backups) | Proxmox VE 9.2.3 |

---

## 🗺️ 2026 Roadmap (Updated: July 2026)

### ✅ Completed
- [x] **Zero-Trust Security:** Comprehensive code audit, elimination of hardcoded secrets, closure of LFI vulnerabilities.
- [x] **Smart Asynchronous Logic:** Implementation of async caching (FastAPI) to prevent deadlocks.
- [x] **Power-Safety-UA v3 Evolution:** Full migration from Flash Monitor to Power-Safety-UA (FastAPI + Docker). Version v3.9.2.
- [x] **Niftywall v3 Rewrite:** Rewritten in TypeScript with full nftables support + Fail2Ban analytics.
- [x] **SEO Initiative:** Web presence optimization for 20+ repositories (robots.txt, sitemap, JSON-LD, topics).
- [x] **Infrastructure Consolidation:** Decommissioned IONOS, SRVRS-ONLINE, PRXMX-03. Consolidated on HTZNR + PRXMX-01/02.

### 🔄 In Progress
- [ ] **Infrastructure as Code (IaC):** Full transition to Ansible playbooks for idempotent management across all servers (HTZNR, PRXMX-01, PRXMX-02).
- [ ] **High Availability (HA):** Failover cluster between HTZNR and PRXMX-01 for uninterrupted Power-Safety-UA operation if the primary datacenter goes down.
- [ ] **AI-Driven Analytics:** Integrate Gemini / LLMs for automated analysis of Fail2Ban logs and Niftywall metrics (infrastructure self-healing).
- [ ] **IPv6 Rollout & Advanced WAF:** Full IPv6 stack deployment and hardened Cloudflare WAF rules for PWA dashboards.

### 🧠 Local LLM & AI Agents (Q3–Q4 2026)
- [ ] **Local LLM Inference Stack:** Deploy Ollama + Open WebUI + LiteLLM on PRXMX-02 for private AI with Qwen2.5 / DeepSeek models.
- [ ] **AI Agentic Automation:** n8n + local LLM for autonomous log analysis (Fail2Ban, Niftywall), infrastructure self-healing, and intelligent alerting.
- [ ] **Self-Sovereign AI:** All AI inference stays local — zero data leaves the homelab, full privacy and control.

### 📈 Infrastructure & Monitoring (Q4 2026)
- [ ] **Unified Observability Stack:** Prometheus + Grafana + Netdata for all node metrics, AI workloads, and Power-Safety-UA.
- [ ] **K3s Container Orchestration:** Migrate Docker Compose services to lightweight Kubernetes (K3s) for scalability and resilience.
- [ ] **AI-Driven Capacity Planning:** Automated trend analysis of CPU/RAM/disk usage for upgrade forecasting.

---

<br>
<p align="center">
  Built in Ukraine under air raid sirens &amp; blackouts ⚡<br>
  &copy; 2026 Weby Homelab
</p>

<!--
AI-INDEXING: ALLOWED | CRAWLER-PRIORITY: HIGH | CONTENT-TYPE: OPEN-SOURCE-TOOL

@context: https://schema.org
@type: SoftwareApplication
name: Homelab — Infrastructure as Code
alternateName: homelab
description: The central nervous system of my infrastructure. Infrastructure as Code (IaC), configurations, automation scripts, and monitoring setups for my secure, multi-node cloud and local HomeLab environment.
applicationCategory: WebApplication
applicationSubCategory: Infrastructure
operatingSystem: Linux
softwareVersion: 1.0.0
keywords: homelab, infrastructure, iac, ansible, automation, docker, security, self-hosted, monitoring, proxmox, tailscale, devops, seo, github
author: Weby Homelab (https://github.com/weby-homelab)
codeRepository: https://github.com/weby-homelab/homelab
downloadUrl: https://github.com/weby-homelab/homelab/releases
license: GPL-3.0
isAccessibleForFree: true
-->
