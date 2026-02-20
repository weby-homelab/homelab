# 🌌 Weby HomeLab: The Infrastructure Matrix

Welcome to the central repository for my home lab and cloud infrastructure. This is not just a collection of servers — it is an automated, secure, and resilient ecosystem that connects public cloud nodes with local Proxmox clusters via encrypted tunnels.

This repository stores the code, configurations (Infrastructure as Code), and architectural decisions that drive everything: from monitoring air quality and power outages in Kyiv to running secure VoIP telephony.

Also available in [Ukrainian (Українська)](README.md).

---

## 🏗 System Architecture

The infrastructure is logically divided into multiple zones that seamlessly communicate with each other through **Tailscale Mesh VPN** and **Cloudflare Tunnels**.

### ☁️ Cloud Edge (Hetzner & IONOS)
The public perimeter handling external traffic. It is heavily secured using `NFTables` (Drop Policy) and `Fail2Ban`.
*   **Services:** Docker hosts, Asterisk VoIP (with strict PJSIP rate limiting), Turnserver, Uptime Kuma for external monitoring.
*   **Access:** Non-standard SSH ports (54322), strictly ED25519 key-based authentication, and forced `tmux` sessions for session reliability.

### 🏠 Local Core (Proxmox VE Cluster)
The heart of the home lab. A virtualized environment managing internal networks and data storage.
*   **Nodes:** A reliable primary server (pve02) and a backup node based on a laptop (pve01) with automated battery monitoring.
*   **Containers (LXC):** Network security (AdGuard Home), IT-Tools, NAS (SMB/NFS), and isolated development environments.

---

## 🚀 Flagship Projects

These projects are already deployed, actively running, and providing daily value.

### 🔆 Light Monitor Kyiv (v1.2.1)
*   **Status:** 🟢 Active | [Repository](https://github.com/weby-homelab/light-monitor-kyiv)
*   **Concept:** An intelligent power monitoring system featuring a Web dashboard (Plan vs Fact charts) and a Telegram bot.
*   **Features:** 48-hour schedule analysis, detection of schedule deviations (accuracy of power on/off events), and generation of detailed daily/weekly reports.

### 🛡️ Security Monitor Kyiv (v1.2.1)
*   **Status:** 🟢 Active | [Repository](https://github.com/weby-homelab/security-monitor-kyiv)
*   **Concept:** Internal security dashboard (accessible only via secure tunnel).
*   **Features:** Real-time air quality metrics (AQI, PM2.5/10 via OpenMeteo and SaveEcoBot), integration with Ubilling API, and a live alert map (alerts.in.ua).

### 📞 Secure VoIP Core
*   **Status:** 🟢 Active (Containerized) | [Repository](https://github.com/weby-homelab/voip-installer)
*   **Concept:** Deployment of highly secure Asterisk servers using Docker. Features automated installation, backups, and anti-bruteforce protection at the network core level (NFTables).

---

## 🗺️ Roadmap & Plans

The project is continuously evolving. The structure of this repository serves as the foundation for future improvements:

### Phase 1: Infrastructure as Code (IaC) 🛠️
*   [ ] **Ansible (`configs/`):** Transitioning from bash scripts to playbooks for automated deployment of NFTables rules, SSH hardening, and Docker environments.
*   [ ] **Terraform:** Declarative provisioning of VPS instances on Hetzner and IONOS.

### Phase 2: Deep Observability 📊
*   [ ] Deploying the **Grafana + Prometheus + Loki** stack.
*   [ ] Centralizing telemetry from Proxmox (temperatures, disk wear) and cloud servers (SWAP, RAM loads) into a single unified dashboard.

### Phase 3: CI/CD Pipelines (`pipelines/`) ⚙️
*   [ ] Setting up GitHub Actions for automated code testing before deployment (e.g., Python code linting in `light-monitor-kyiv`).
*   [ ] Automated building and pushing of Docker images for custom services.

### Phase 4: High Availability (HA) & Backups 💾
*   [ ] Fully automated cloud backups for LXC container configurations.
*   [ ] Disaster Recovery scenario testing (restoring infrastructure from scratch using stored configurations).

---

## 📂 Repository Structure

*   📂 `docs/` — Architectural Decision Records (ADR), guides, and documentation.
*   📂 `configs/` — Configuration files (Ansible, Docker Compose, service configs).
*   📂 `scripts/` — Automation utilities (bash/python scripts for security audits, SWAP monitoring, etc.).
*   📂 `pipelines/` — CI/CD configuration files (GitHub Actions).
*   📂 `diagrams/` — Network topologies and architectural diagrams.
*   📂 `tests/` — Scripts for testing infrastructure security and integrity.

---
> *"Automate everything you do twice. Monitor everything that matters."*