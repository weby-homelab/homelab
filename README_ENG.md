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

The project is continuously evolving. The repository structure is designed to support modern DevOps/SRE practices:

### Phase 1: Infrastructure as Code (IaC) & Secret Management 🛠️
*   [ ] **Ansible (`configs/`):** Declarative management of server configurations (NFTables, SSH hardening, Docker environments) instead of manual bash scripts.
*   [ ] **Terraform / OpenTofu:** Automated provisioning and management of cloud resources on Hetzner and IONOS.
*   [ ] **Secret Management:** Implementing SOPS or HashiCorp Vault to securely store passwords and API keys within Git.

### Phase 2: Deep Observability 📊
*   [ ] Deploying the **Prometheus + Grafana + Loki + Promtail** stack.
*   [ ] Centralizing logs and telemetry (SWAP, RAM, temperatures, disk wear) from all nodes (Proxmox, Cloud VPS) into a unified dashboard.
*   [ ] Setting up automated alerting via Telegram (using Alertmanager).

### Phase 3: GitOps & CI/CD Automation (`pipelines/`) ⚙️
*   [ ] **GitHub Actions:** Automated linting (Python, Bash) and security auditing (Trivy/Dependabot) prior to deployment.
*   [ ] Automated building and publishing of custom Docker images (e.g., to GHCR).
*   [ ] **GitOps:** Implementing mechanisms for automatic container updates on servers upon repository changes (Watchtower or hybrid pipelines).

### Phase 4: High Availability (HA) & Disaster Recovery 💾
*   [ ] Expanding **Zero Trust** access (configuring Tailscale ACLs and Cloudflare Zero Trust).
*   [ ] Automated incremental backups (via Proxmox Backup Server or Restic) to encrypted cloud storage.
*   [ ] Regular testing of Disaster Recovery scenarios (Bare Metal Recovery using stored IaC configurations).

---

## 📂 Repository Structure

*   📂 `docs/` — Architectural Decision Records (ADR), guides, and recovery playbooks.
*   📂 `configs/` — IaC configurations (Ansible, Terraform, Docker Compose files).
*   📂 `scripts/` — Utilities (security audit scripts, custom monitoring, backup scripts).
*   📂 `pipelines/` — CI/CD configuration files (GitHub Actions) and GitOps manifests.
*   📂 `diagrams/` — Network topologies and architectural diagrams (Mermaid/Draw.io).
*   📂 `tests/` — Automated tests for infrastructure and security policies.

---
> *"Automate everything you do twice. Monitor everything that matters."*