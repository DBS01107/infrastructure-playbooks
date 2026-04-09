#  DNS Playbook (Domain Resolution & Reliability)

Production-ready, failure-aware guide for DNS configuration and troubleshooting.

---

## 1. DNS Basics (Mental Model)

DNS maps:

```
your_domain.com → server IP
```

Critical for:

* Accessibility
* SSL issuance
* Reverse proxy routing

---

## 2. Nameserver Configuration

### Set Nameservers

* Configure at domain registrar
* Use provider nameservers (Cloudflare / hosting provider)

### Verify

```bash
dig your_domain.com NS
```

**Failures:**

* ❌ Not updated → wait for propagation (can take hours)

---

## 3. Core DNS Records

### A Record (Root Domain)

* Name: @
* Value: VPS IP

### CNAME (WWW)

* Name: www
* Value: your_domain.com

---

### Verify Resolution

```bash
dig your_domain.com +short
```

```bash
nslookup your_domain.com
```

✔ Should return correct server IP

---

## 4. TTL (Time To Live)

* Controls DNS cache duration

Typical values:

* 300 (fast updates)
* 3600 (standard)

 Lower TTL during migrations

---

## 5. DNS + Architecture Patterns

### 🔹 Case 1: Separate Frontend Hosting + VPS Backend

* Main domain (`your_domain.com`) → points to frontend hosting (Vercel/Netlify/CDN)
* API subdomain (`api.your_domain.com`) → points to VPS

Example:

* `your_domain.com` → frontend
* `api.your_domain.com` → backend (VPS)

 This is the recommended modern architecture

---

### 🔹 Case 2: Everything on Same VPS

* Domain (`your_domain.com`) → points to VPS
* Reverse proxy handles routing:

  * `/` → frontend
  * `/api` → backend

Example flow:

```
your_domain.com → VPS → Nginx/Apache → frontend + /api → backend
```

---

### Request Flow (Generic)

````
User → DNS → Domain → Server IP → Reverse Proxy → App
```}]}

````

User → DNS → Domain → Server IP → Reverse Proxy → App

````

---

## 6. Common Failures

### ❌ Domain Not Resolving
- Check nameservers
- Wait for propagation

### ❌ Wrong IP
- Incorrect A record

### ❌ Works on some networks only
- DNS caching issue

```bash
sudo systemd-resolve --flush-caches
````

---

## 7. Debugging DNS

### Check Full Trace

```bash
dig +trace your_domain.com
```

### Check Specific Resolver

```bash
dig @8.8.8.8 your_domain.com
```

---

## 8. DNS + SSL Dependency

SSL (Certbot) requires:

* Domain resolves correctly
* Port 80 accessible

---

## 9. Final Validation Checklist

* ✅ Nameservers correct
* ✅ A record pointing to server
* ✅ WWW working
* ✅ Domain resolves globally
* ✅ No stale cache issues

---

## 10. Emergency Fix

* Re-check A record
* Flush local DNS cache
* Use alternate resolver (8.8.8.8)

---

This playbook ensures reliable domain resolution and proper DNS configuration for production systems.
