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
  <img src="https://img.shields.io/badge/Updated-July%202026-purple?style=for-the-badge" alt="Updated">
</p>

Ласкаво просимо до центрального вузла екосистеми **Weby Homelab** — автоматизованої, безпечної та відмовостійкої інфраструктури, що об'єднує хмарні ресурси та локальні кластери в єдиний живий організм.

Тут зберігається інтелект моєї лабораторії: від конфігурацій безпеки, моніторингу трафіку до систем оповіщення про критичні ситуації в Києві.

Доступна також [Англійська версія документації](README_ENG.md).

---

## 🏗 Архітектура Екосистеми (Mega-Topology)

Наша інфраструктура розгорнута за принципами **Hybrid Cloud**, **Zero Trust** та **Secure by Design**. Всі вузли зв'язані через **Tailscale Mesh VPN** (приватні IP, без публічних точок входу), керуються через централізовані правила Niftywall, та захищені **Cloudflare Tunnels**.

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
    subgraph HTZNR ["☁️ HTZNR (Cloud PROD — Hetzner, Tailscale: 100.83.52.116)"]
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
    subgraph PRXMX01 ["🏠 PRXMX-01 (Home Core — Proxmox, Tailscale: 100.86.120.114)"]
        direction TB
        LXC200["🐳 LXC 200 (Docker, 100.124.218.39)"]:::service
        ADG["🛑 ADBlock-PD (DNS)"]:::network
        AQD["🌤️ Air Quality Dashboard"]:::service
        PING["📡 Outage Ping Scripts"]:::service
    end

    %% -- Node: PRXMX-02 (Home Backup) --
    subgraph PRXMX02 ["🏠 PRXMX-02 (Home Backup — Proxmox, Tailscale: 100.122.16.1)"]
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

## 🚀 Основні проекти (Оновлено: Липень 2026)

Екосистема складається з кількох незалежних, але інтегрованих модулів, що працюють як єдине ціле:

### ⚡ [Power-Safety-UA](https://github.com/weby-homelab/Power-Safety-UA) (Флагман)
**Уніфікована автономна система енергомоніторингу та безпеки.**
- **Статус:** 🟢 **Active v3.9.2** (Docker Edition)
- **Суть:** Об'єднання функцій моніторингу світла, повітряних тривог, якості повітря (AQI), радіаційного фону. "Спокійний режим" (Quiet Mode) та "Safety Net" (35с таймаут пушу).
- **Особливість:** PWA-панель, Glassmorphism Admin Panel, асинхронне кешування (відсутність дедлоків), висока безпека (усунуто LFI).
- **Еволюція:** Наступник Flash Monitor Kyiv (v2.x) — повністю переписаний на FastAPI + Docker.

### 🔥 [Firewalld-GUI](https://github.com/weby-homelab/firewalld-gui) та [Niftywall](https://github.com/weby-homelab/niftywall)
**Системи мережевого захисту та Zero-Trust фільтрації.**
- **Статус:** 🟢 **Active (v1.6.13 та v3.4.0)**
- **Суть:** Firewalld-GUI надає графічний веб-інтерфейс для керування зонами та портами, тоді як Niftywall (переписаний на TypeScript) відповідає за безпосереднє застосування низькорівневих nftables-правил та аналітику Fail2Ban.
- **Безпека:** Оновлено управління секретами, заблоковано атаки Path Traversal, безпечна генерація JWT токенів.

### 📞 [VoIP Installer](https://github.com/weby-homelab/voip-installer)
- **Суть:** Автоматизоване розгортання захищеної телефонії Asterisk у Docker (v4.7.9). Захищено через Fail2Ban (asterisk-pjsip) та повний стек TLS/SRTP.

### 🧠 [AI Second Brain GUI](https://github.com/weby-homelab/ai-second-brain-gui)
- **Суть:** Веб-інтерфейс Obsidian (Second Brain) для доступу, пошуку та моніторингу бази знань. Glassmorphism-дизайн на FastAPI.

### 📧 [Docker Mailserver GUI](https://github.com/weby-homelab/docker-mailserver-gui)
- **Суть:** Zero-trust поштовий сервер з Traefik проксі, SnappyMail GUI та повним стеком безпеки.

### 🛡️ [ADBlock-PD](https://github.com/weby-homelab/ADBlock-PD)
- **Суть:** Хардкорний форк AdGuard Home з нульовою телеметрією. Повністю розірвано зв'язки з інфраструктурою AdGuard.

### 🤖 [Safety Chat Bot](https://github.com/weby-homelab/safety-chat-bot)
- **Суть:** Telegram-бот модерації чатів з капчею, адмін-сповіщеннями та Aiogram 3.

### 🛡️ Архівовані Проєкти (Інтегровані)
- **Light Monitor Kyiv / Security Monitor Kyiv:** Функціонал повністю поглинуто Power-Safety-UA v3+.
- **UFW GUI:** Замінено на Firewalld-GUI та Niftywall задля кращої стабільності в Docker.
- **Flash Monitor Kyiv (fm-ua):** Перейменовано та переписано на Power-Safety-UA (FastAPI + Docker).
- **IONOS / SRVRS-ONLINE / PRXMX-03:** Декомісійовано (2026). Інфраструктура консолідована на HTZNR + PRXMX-01/02.

---

## 🖥️ Апаратний Стек (Липень 2026)

| Вузол | Tailscale IP | Роль | ОС / Гіпервізор |
| :--- | :--- | :--- | :--- |
| **HTZNR** | `100.83.52.116` | Cloud PROD (Power-Safety-UA, Niftywall, VoIP, Kuma) | Ubuntu 24.04 LTS (Bare Metal) |
| **PRXMX-01** | `100.86.120.114` | Home Core (LXC 200 Docker, ADBlock-PD, Ping Scripts) | Proxmox VE 9.2.3 |
| **LXC 200** | `100.124.218.39` | Docker Testbed (Power-Safety-UA staging, Air Quality) | Debian LXC on PRXMX-01 |
| **PRXMX-02** | `100.122.16.1` | Home Backup (Samba NAS, Transmission, Pull Backups) | Proxmox VE 9.2.3 |

---

## 🗺️ Дорожня карта 2026 (Оновлено: Липень 2026)

- [x] **Zero-Trust Security:** Глобальний аудит коду, усунення хардкод-секретів, закриття LFI вразливостей.
- [x] **Smart Asynchronous Logic:** Впровадження асинхронного кешу (FastAPI) для запобігання дедлокам.
- [x] **Power-Safety-UA v3 Evolution:** Повний перехід з Flash Monitor на Power-Safety-UA (FastAPI + Docker). Версія v3.9.2.
- [x] **Niftywall v3 Rewrite:** Перепис на TypeScript з повною підтримкою nftables + Fail2Ban аналітики.
- [x] **SEO Initiative:** Оптимізація веб-присутності всіх 20+ репозиторіїв (robots.txt, sitemap, JSON-LD, topics).
- [x] **Infrastructure Consolidation:** Декомісія IONOS, SRVRS-ONLINE, PRXMX-03. Консолідація на HTZNR + PRXMX-01/02.
- [ ] **Infrastructure as Code (IaC):** Повний перехід на Ansible плейбуки для забезпечення ідемпотентності всіх серверів (HTZNR, PRXMX-01, PRXMX-02).
- [ ] **High Availability (HA):** Налаштування failover-кластера між HTZNR та PRXMX-01 для безперебійної роботи Power-Safety-UA у разі падіння основного ЦОД.
- [ ] **AI-Driven Analytics:** Впровадження Gemini / LLM для автоматичного аналізу логів Fail2Ban та метрик Niftywall (самолікування інфраструктури).
- [ ] **IPv6 Rollout & Advanced WAF:** Повне розгортання IPv6-стеку та посилення правил Cloudflare WAF для PWA панелей.

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
