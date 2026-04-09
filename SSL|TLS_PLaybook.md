#  SSL/TLS Playbook (Certbot + HTTPS Enforcement)

Production-grade SSL setup with automation, renewal, and failure-aware handling.

---

## 0. Pre-flight Checks

* Domain points to server (DNS OK)
* Ports 80 & 443 open
* Nginx/Apache running
* No conflicting services on port 80

### Quick Verifications

```bash
dig your_domain.com +short
```

✔ Should resolve to your server IP

```bash
ufw allow 80
ufw allow 443
ufw reload
```

---

## 1. Install Certbot

### Nginx

```bash
apt install certbot python3-certbot-nginx
```

### Apache

```bash
apt install certbot python3-certbot-apache
```

### Verify

```bash
certbot --version
```

**Failures:**

* ❌ Package not found → run `apt update`

---

## 2. Issue SSL Certificate

### Guided Installation (Recommended)

Use this for standard Nginx/Apache setups.

#### Nginx (Auto-configure SSL + Redirect)

```bash
certbot --nginx -d your_domain.com -d www.your_domain.com
```

#### Apache (Auto-configure SSL + Redirect)

```bash
certbot --apache -d your_domain.com -d www.your_domain.com
```

✔ This will:

* Issue certificate
* Configure HTTPS (port 443)
* Setup HTTP → HTTPS redirect

---

### Manual Certificate Issue (Advanced / Custom setups)

```bash
certbot certonly --standalone -d your_domain.com
```

(Use this when you want full control over reverse proxy configs)

---

### Common Failures

* ❌ Domain not reachable → check DNS
* ❌ Port 80 blocked → allow in firewall
* ❌ Nginx config invalid → run `nginx -t`

---

## 3. Force HTTPS

Certbot usually adds this automatically.

### Verify Redirect

```bash
curl -I http://your_domain.com
```

**Expected:** 301 → https

**Failures:**

* ❌ No redirect → manually add in reverse proxy config

---

## 4. TLS Hardening (Recommended)

### Nginx

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_ciphers HIGH:!aNULL:!MD5;
```

### HSTS (Optional but Recommended)

```nginx
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
```

### OCSP Stapling (Advanced)

```nginx
ssl_stapling on;
ssl_stapling_verify on;
```

---

## 5. Auto Renewal

### Test Renewal

```bash
certbot renew --dry-run
```

### Renewal Hook (Zero-downtime reload)

```bash
certbot renew --deploy-hook "systemctl reload nginx"
```

(Use apache instead of nginx if applicable)

### Check Timer

```bash
systemctl list-timers | grep certbot
```

**Failures:**

* ❌ Renewal fails → check logs:

```bash
tail -f /var/log/letsencrypt/letsencrypt.log
```

---

## 6. Certificate Paths

```bash
/etc/letsencrypt/live/your_domain.com/fullchain.pem
/etc/letsencrypt/live/your_domain.com/privkey.pem
```

---

## 7. Core SSL Checks

### Verify HTTPS

```bash
curl -I https://your_domain.com
```

### Check Certificate

```bash
openssl s_client -connect your_domain.com:443
```

### Browser Validation

* Open site → verify 🔒 icon
* Optional: SSL Labs test

---

## 8. Rate Limits (Important)

* Let’s Encrypt limit: **50 certs/week/domain**

### Use staging for testing

```bash
certbot --staging --nginx -d your_domain.com
```

---

## 9. Common Failures

* ❌ SSL not working → wrong cert path in config
* ❌ Mixed content → frontend calling HTTP resources
* ❌ Renewal broken → port 80 blocked
* ❌ Rate limit → too many requests

---

## 10. Final Validation Checklist

* ✅ SSL certificate issued
* ✅ HTTPS working (443)
* ✅ HTTP → HTTPS redirect
* ✅ Auto-renewal configured
* ✅ Renewal test passed

---

## 11. Emergency Recovery

* Re-run certbot
* Temporarily disable HTTPS config
* Restore backup config

---

This playbook ensures encrypted communication and automated certificate lifecycle management.
