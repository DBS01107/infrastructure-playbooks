# 🖥️ Server Documentation

## 1. Overview

- Server Name:
- Provider (AWS/DigitalOcean/etc):
- Region:
- OS:
- Public IP:
- Purpose:

---

## 2. Access & Security

### SSH
- Port:
- Root Login: Disabled/Enabled
- Key-based Auth: Yes/No

### Firewall (UFW)
- Allowed Ports:
- Default Policy:

### Security Tools
- Fail2Ban: Enabled/Disabled
- Unattended Upgrades: Yes/No

---

## 3. Installed Stack

### System Packages
- Nginx / Apache / OpenLiteSpeed:
- Node.js:
- Python:
- Java:
- Docker:

---

## 4. Running Services

| Service Name | Port | Description | Managed By |
|-------------|------|------------|-----------|
| app         | 3000 | Backend API | systemd |
| nginx       | 80/443 | Reverse proxy | systemd |

---

## 5. Directory Structure

```
/var/www/
├── app/
├── frontend/
├── logs/
```

---

## 6. Reverse Proxy Configuration

- Config Location:
- Domains:
- Routing:

Example:
- `/` → frontend
- `/api` → backend

---

## 7. Environment Configuration

- `.env` location:
- Permissions:
- Secrets handling approach:

---

## 8. Logging

- App Logs:
- Nginx Logs:
- System Logs:

---

## 9. Backup Configuration

- Backup Location:
- Remote Backup:
- Snapshot Enabled:

---

## 10. Monitoring (if any)

- Tools:
- Alerts:

---

## 11. Notes

- Known issues:
- Special configurations:
