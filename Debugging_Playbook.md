#  Debugging Playbook (Production Issues)

A systematic, failure-aware checklist to diagnose and resolve production issues across application, infrastructure, and network layers.

---

## 1. Check Service Status

```bash
systemctl status app
```

**Failures:**

* ❌ Service not running → `systemctl restart app`
* ❌ Crash loop → check logs immediately

---

## 2. Check Logs (Multi-layer)

### Application Logs

* PM2:

```bash
pm2 logs
```

* Systemd:

```bash
journalctl -u app -f
```

### Reverse Proxy Logs

**Nginx**

```bash
tail -f /var/log/nginx/error.log
```

**Apache**

```bash
tail -f /var/log/apache2/error.log
```

---

## 3. Check Ports & Bindings

```bash
ss -tulnp
```

**Failures:**

* ❌ Port not listening → app not running
* ❌ Port conflict → identify process:

```bash
lsof -i :PORT
```

---

## 4. Verify Environment Variables

* Check `.env` file
* Check systemd env config

```bash
printenv | grep VAR_NAME
```

**Failures:**

* ❌ Missing env → app crashes
* ❌ Wrong values → misbehavior

---

## 5. Check Permissions

```bash
ls -la /var/www/app
```

**Failures:**

* ❌ Permission denied → fix ownership:

```bash
chown -R www-data:www-data /var/www/app
```

---

## 6. Test Locally (Bypass Proxy)

```bash
curl localhost:PORT
```

✔ If works → issue is in proxy/DNS/SSL

---

## 7. Check DNS / Domain

```bash
dig your_domain.com +short
```

**Failures:**

* ❌ Wrong IP → DNS misconfigured
* ❌ No response → propagation issue

---

## 8. Check SSL Validity

```bash
curl -I https://your_domain.com
```

```bash
openssl s_client -connect your_domain.com:443
```

**Failures:**

* ❌ Expired cert → renew certbot
* ❌ Handshake error → config issue

---

## 9. Identify Bottlenecks

### CPU

```bash
top
```

### Memory

```bash
free -h
```

### Disk

```bash
df -h
```

### Network

```bash
iftop
```

**Failures:**

* ❌ High CPU → inefficient code / loops
* ❌ High RAM → memory leak
* ❌ Disk full → cleanup logs/cache

---

## 10. Isolation Strategy (Golden Rule)

Break the system into layers:

1. App (localhost)
2. Reverse Proxy
3. DNS / Domain
4. SSL / CDN

👉 Test each layer independently to isolate failure

---

## 11. Final Debug Checklist

* ✅ Service running
* ✅ Logs clean / readable
* ✅ Ports open & correct
* ✅ Env variables correct
* ✅ Permissions correct
* ✅ Local app working
* ✅ DNS resolving
* ✅ SSL valid
* ✅ No resource bottleneck

---

## 12. Emergency Recovery

* Restart service
* Rollback deployment
* Disable proxy temporarily
* Check logs immediately

---

This playbook provides a structured approach to diagnosing and resolving production issues efficiently.
