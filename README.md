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
  <img src="https://img.shields.io/badge/Updated-April%202026-yellow?style=for-the-badge" alt="Updated">
</p>

Ласкаво просимо до центрального вузла екосистеми **Weby Homelab** — автоматизованої, безпечної та відмовостійкої інфраструктури, що об'єднує хмарні ресурси та локальні кластери в єдиний живий організм.

Тут зберігається інтелект моєї лабораторії: від конфігурацій безпеки, моніторингу трафіку до систем оповіщення про критичні ситуації в Києві.

Доступна також [Англійська версія документації](README_ENG.md).

---

## 🏗 Архітектура Екосистеми (Mega-Topology)

Наша інфраструктура розгорнута за принципами **Hybrid Cloud**, **Zero Trust** та **Secure by Design**. Всі вузли зв'язані через **Tailscale Mesh VPN**, керуються через централізовані правила (Niftywall / Firewalld), та захищені **Cloudflare Tunnels**.

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

## 🚀 Основні проекти (Оновлено: Квітень 2026)

Екосистема складається з кількох незалежних, але інтегрованих модулів, що працюють як єдине ціле:

### ⚡ [Flash Monitor Kyiv](https://github.com/weby-homelab/flash-monitor-kyiv) (Флагман)
**Уніфікована автономна система енергомоніторингу та безпеки.**
- **Статус:** 🟢 **Active v3.2.1**
- **Суть:** Об'єднання функцій моніторингу світла, повітряних тривог, якості повітря (AQI). "Спокійний режим" (Quiet Mode) та "Safety Net" (35с таймаут пушу).
- **Особливість:** PWA-панель (admin.srvrs.top), асинхронне кешування (відсутність дедлоків), висока безпека (усунуто LFI).

### 🔥 [Firewalld-GUI](https://github.com/weby-homelab/firewalld-gui) та [Niftywall](https://github.com/weby-homelab/niftywall)
**Системи мережевого захисту та Zero-Trust фільтрації.**
- **Статус:** 🟢 **Active (v1.6.0 та v1.5.0)**
- **Суть:** Firewalld-GUI надає графічний веб-інтерфейс для керування зонами та портами, тоді як Niftywall відповідає за безпосереднє застосування низькорівневих nftables-правил та аналітику Fail2Ban.
- **Безпека:** Оновлено управління секретами, заблоковано атаки Path Traversal, безпечна генерація JWT токенів.

### 📞 [VoIP Installer](https://github.com/weby-homelab/voip-installer)
- **Суть:** Автоматизоване розгортання захищеної телефонії Asterisk у Docker (v4.6.x). Захищено через Fail2Ban (asterisk-pjsip).

### 🛡️ Архівовані Проєкти (Інтегровані)
- **Light Monitor Kyiv / Security Monitor Kyiv:** Функціонал повністю поглинуто Flash Monitor v3.2+.
- **UFW GUI:** Замінено на Firewalld-GUI та Niftywall задля кращої стабільності в Docker.

---

## 🖥️ Апаратний Стек (Квітень 2026)

| Вузол | Локація | Роль | ОС / Гіпервізор |
| :--- | :--- | :--- | :--- |
| **HTZNR (Primary)** | Німеччина | Prod Edge (Flash, Niftywall, Arcane) | Ubuntu 24.04 LTS (Bare Metal) |
| **PRXMX-02-LXC200**| Home Lab (Київ)| Prod Pings, Docker Testbed, AdGuard| Proxmox VE 9.1 (Tailscale IP)|
| **IONOS** | Європа | Docker Test Node, Backup | Debian (Public IP) |
| **SRVRS-ONLINE** | Європа | Secondary Backup | Ubuntu (Public IP) |

---

## 🗺️ Дорожня карта 2026 (Оновлена)

- [x] **Zero-Trust Security:** Глобальний аудит коду, усунення хардкод-секретів, закриття LFI вразливостей.
- [x] **Smart Asynchronous Logic:** Впровадження асинхронного кешу (FastAPI) для запобігання дедлокам у Flash Monitor.
- [ ] **Infrastructure as Code (IaC):** Повний перехід на Ansible плейбуки для забезпечення ідемпотентності всіх серверів (HTZNR, PRXMX, IONOS).
- [ ] **High Availability (HA):** Налаштування failover-кластера між HTZNR та IONOS для безперебійної роботи Flash Monitor у разі падіння основного ЦОД.
- [ ] **AI-Driven Analytics:** Впровадження Gemini / LLM для автоматичного аналізу логів Fail2Ban та метрик Niftywall (самолікування інфраструктури).
- [ ] **IPv6 Rollout & Advanced WAF:** Повне розгортання IPv6-стеку та посилення правил Cloudflare WAF для PWA панелей.

---
<p align="center">
  ✦ 2026 Weby Homelab ✦ — інфраструктура, що не здається.<br>
  Зроблено з ❤️ у Києві під час сирен та блекаутів...
</p>
