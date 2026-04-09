#  Cloudflare Proxy & SSL Playbook (Free Tier)

Production-grade setup for DNS, proxying, and SSL using Cloudflare (free plan).

---

## 1. Add Domain to Cloudflare

* Go to Cloudflare dashboard
* Add your domain
* Select **Free Plan**

### Update Nameservers

* Replace registrar nameservers with Cloudflare-provided ones

**Verify:**

```bash
dig your_domain.com NS
```

**Failures:**

* ❌ Domain not resolving → wait for DNS propagation

---

## 2. DNS Configuration

### Add A Record

* Type: A
* Name: @
* IP: VPS IP
* Proxy: ✅ Enabled (Orange Cloud)

### Add WWW

* Type: CNAME
* Name: www
* Target: your_domain.com
* Proxy: ✅ Enabled

---

### 🔍 TTL + Proxy Behavior Insight

When proxy is **ON (Orange Cloud):**

* A record returns **Cloudflare IP**, not your server IP
* TTL is managed by Cloudflare (not user-controlled)

👉 Important for debugging origin vs proxy issues

---

## 3. Proxy (Reverse Proxy via Cloudflare)

When proxy is enabled:

* Cloudflare sits between user ↔ your server
* Hides origin IP
* Provides caching + protection

**Verify:**

```bash
curl -I your_domain.com
```

Look for:

```
server: cloudflare
```

---

## 4. SSL Mode Configuration

Go to: **SSL/TLS → Overview**

### Recommended: Full (Strict)

Requires:

* Valid public certificate (Certbot)
  OR
* Cloudflare Origin Certificate

❗ Expired/invalid cert → **525 SSL handshake error**

### Other Modes

* Flexible → ❌ NOT recommended (causes redirect loops)
* Full → allows self-signed cert

---

## 5. Force HTTPS

### Option A: Cloudflare Setting

* SSL/TLS → Edge Certificates
* Enable **Always Use HTTPS**

### Option B: Redirect Rules (Recommended modern approach)

* Create rule → redirect HTTP → HTTPS

---

## 6. Origin SSL Setup (Important)

Even with Cloudflare, origin should have SSL.

Use SSL Playbook (Certbot)

OR

### Cloudflare Origin Certificate

* SSL/TLS → Origin Server → Create Certificate
* Install on server (Nginx/Apache)

---

## 7. Security Enhancements (Free Tier)

* Enable **Proxy (Orange Cloud)** for all records
* Turn on **Bot Fight Mode**
* Security Level: Medium

---

## 8. Origin Lockdown (Highly Recommended)

Prevent direct access to your server IP:

* Allow only Cloudflare IP ranges in firewall
* Block all other inbound traffic

This prevents:

* DDoS bypass
* Direct IP attacks

---

## 9. Common Failures

### ❌ 522 Timeout

* Ensure server is running
* Allow Cloudflare IP ranges in firewall
* Check ports open:

```bash
ufw allow 80
ufw allow 443
```

### ❌ 525 SSL Handshake

* Invalid/expired origin cert

### ❌ Redirect Loop

* Using Flexible SSL + server redirect

---

## 10. Real IP Configuration (Nginx)

Cloudflare masks client IPs by default.

Without this:

* Logs show Cloudflare IPs
* Rate limiting & analytics break

```nginx
# Must include ALL Cloudflare IP ranges (not just one)
set_real_ip_from 103.21.244.0/22;
real_ip_header CF-Connecting-IP;
```

❗ Add all Cloudflare IP ranges from official docs

---

## 11. Cache Behavior

Cloudflare may cache static content automatically.

* Use `Cache-Control` headers for control
* Configure cache rules if needed

---

## 12. Debugging (Bypass Cloudflare)

Test origin directly:

```bash
curl -I http://your_server_ip
```

✔ Helps isolate:

* Cloudflare issue vs origin issue

---

## 13. Final Validation Checklist

* ✅ DNS pointing to Cloudflare
* ✅ Proxy enabled (orange cloud)
* ✅ SSL mode = Full (Strict)
* ✅ HTTPS enforced
* ✅ Origin SSL configured
* ✅ No redirect loops

---

## 14. Emergency Recovery

* Disable proxy (grey cloud) temporarily
* Switch SSL mode to Full
* Check origin server status

---

This playbook ensures secure, proxied, and protected traffic using Cloudflare free tier.
