# Weby Homelab: Global Security Policy

## 🛡️ Zero-Trust Philosophy

Weby Homelab projects are designed for critical infrastructure monitoring and automation. We follow a strict **Zero-Trust** policy regarding configuration and credentials.

### 📜 Security Rules
1. **Infrastructure as Code (IaC):** All deployments are pull-based and verified via tests.
2. **Secret Management:** Hardcoded tokens are prohibited. Use `.env` or system environment variables.
3. **Audit Trails:** All systems must maintain audit logs for sensitive operations.

---

## Reporting a Security Vulnerability

If you find a vulnerability in any project within the Weby Homelab ecosystem:
- **Primary contact:** rekvizitor.ua@gmail.com
- **Secondary contact (TG):** @rekvizitor_ua

---

## Загальна політика безпеки (UA)

Всі проекти Weby Homelab базуються на принципі **Zero-Trust**:
- Жодних захардкоджених токенів.
- Тільки асинхронна та безпечна робота з даними.
- При виявленні дірок у безпеці — пишіть напряму розробнику.
