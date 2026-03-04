<p align="center">
  <a href="README_ENG.md">
    <img src="https://img.shields.io/badge/🇬🇧_English-00D4FF?style=for-the-badge&logo=readme&logoColor=white" alt="English README">
  </a>
  <a href="README.md">
    <img src="https://img.shields.io/badge/🇺🇦_Українська-FF4D00?style=for-the-badge&logo=readme&logoColor=white" alt="Українська версія">
  </a>
</p>

<br>

# 🌌 Weby Homelab: Інфраструктурна Матриця

<p align="center">
  <img src="https://img.shields.io/badge/Infrastructure-as--Code-blueviolet?style=for-the-badge&logo=ansible" alt="IaC">
  <img src="https://img.shields.io/badge/Security-Zero--Trust-red?style=for-the-badge&logo=cloudflare" alt="Security">
  <img src="https://img.shields.io/badge/Status-Active-success?style=for-the-badge" alt="Status">
  <img src="https://img.shields.io/badge/2026-Ready-yellow?style=for-the-badge" alt="Year">
</p>

Ласкаво просимо до центрального вузла екосистеми **Weby Homelab** — автоматизованої, безпечної та відмовостійкої інфраструктури, що об'єднує хмарні ресурси та локальні кластери в єдиний живий організм.

Тут зберігається інтелект моєї лабораторії: від конфігурацій безпеки до систем моніторингу критичної ситуації в Києві.

Доступна також [Англійська версія документації](README_ENG.md).

---

## 🏗 Архітектура Екосистеми

Наша інфраструктура побудована на принципах **Hybrid Cloud** та **Zero Trust**. Всі вузли зв'язані через **Tailscale Mesh VPN** та захищені **Cloudflare Tunnels**.

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

## 🚀 Основні проекти

Екосистема складається з кількох незалежних, але інтегрованих модулів:

### ⚡ [Flash Monitor Kyiv](https://github.com/weby-homelab/flash-monitor-kyiv) (Флагман)
**Уніфікована автономна система енергомоніторингу та безпеки.**
- **Статус:** 🟢 **v1.11.3 Active** (Основна система)
- **Суть:** Повне об'єднання функцій моніторингу світла, повітряних тривог та якості повітря (AQI) в одному Docker-контейнері.
- **Особливість:** Розрахунок точності графіків до секунди, підтримка PWA, автономний парсинг без зовнішніх API.

### 📊 [Light Monitor Kyiv](https://github.com/weby-homelab/light-monitor-kyiv)
- **Статус:** ⛔ **Archived** (Роботу на сервері зупинено, функціонал інтегровано у Flash Monitor).

### 🛡️ [Security Monitor Kyiv](https://github.com/weby-homelab/security-monitor-kyiv)
- **Статус:** ⛔ **Archived** (Роботу на сервері зупинено, функціонал інтегровано у Flash Monitor).

### 📞 [VoIP Installer](https://github.com/weby-homelab/voip-installer)
- **Суть:** Автоматизоване розгортання захищеної телефонії Asterisk 22 у Docker.

---

## 🖥️ Апаратний Стек

| Вузол | Локація | Роль | ОС / Гіпервізор |
| :--- | :--- | :--- | :--- |
| **HTZNR (Primary)** | Німеччина | Edge Services, Flash Monitor | Ubuntu 24.04 LTS |
| **IONOS-VPS** | Європа | Backup VoIP, DNS, Turnserver | Debian (Tmux Hardened) |
| **PRXMX-02** | Home Lab | Центральне ядро, NAS, AdGuard | Proxmox VE 9.1 |
| **PRXMX-01** | Home Lab | Backup Node (Battery Monitored) | Proxmox VE (Laptop) |

---

## 🗺️ Дорожня карта 2026

- [ ] **Infrastructure as Code:** Повний перехід на Ansible плейбуки для всіх серверів.
- [ ] **Secret Management:** Впровадження HashiCorp Vault для безпеки токенів.
- [ ] **Observability:** Стек Prometheus + Grafana для візуалізації стану «заліза».
- [ ] **AI Integration:** Впровадження Gemini API для інтелектуального аналізу логів.

---
<p align="center">
  ✦ 2026 Weby Homelab ✦ — infrastructure that doesn’t give up.<br>
  Made with ❤️ in Kyiv under air raid sirens and blackouts...
</p>
