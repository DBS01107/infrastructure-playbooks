# 🌐 System Architecture

## 1. Overview

Describe the system in one paragraph.

---

## 2. High-Level Architecture

User → DNS → Cloudflare → Server → Reverse Proxy → App

---

## 3. DNS Configuration

| Domain | Type | Points To |
|--------|------|----------|
| domain.com | A | Frontend / VPS |
| api.domain.com | A | VPS |

---

## 4. Cloudflare Setup

- Proxy: Enabled/Disabled
- SSL Mode: Full / Strict
- WAF: Enabled/Disabled

---

## 5. Hosting Strategy

### Option A: Split Architecture

- Frontend: Vercel / Netlify
- Backend: VPS

Routing:
- domain.com → frontend
- api.domain.com → backend

---

### Option B: Single VPS

- domain.com → VPS
- `/` → frontend
- `/api` → backend

---

## 6. Reverse Proxy

- Tool: Nginx / Apache
- Config Path:

Routing:
- `/` → frontend
- `/api` → backend

---

## 7. SSL/TLS

- Provider: Let’s Encrypt / Cloudflare
- Auto-renewal: Yes/No

---

## 8. Data Flow

Example:

User Request:
→ DNS resolves
→ Cloudflare proxy
→ VPS
→ Nginx routes
→ Backend responds

---

## 9. Security Layers

- Firewall (UFW)
- Fail2Ban
- Cloudflare Protection
- HTTPS Enforcement

---

## 10. Failure Points

| Layer | Possible Failure |
|------|----------------|
| DNS | wrong IP |
| Proxy | 502 |
| SSL | expired cert |
| App | crash |

---

## 11. Recovery Strategy

- Debugging playbook reference
- Backup & restore flow

---

## 12. Notes

- Design decisions:
- Tradeoffs:
