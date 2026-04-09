# Reverse Proxy Playbook (Nginx / Apache / OpenLiteSpeed)

Production-grade reverse proxy configuration with failure-aware steps.

---

## 1. Nginx Setup (Recommended)

### Install Nginx

```bash
apt install nginx
nginx -v
```

### Configure Upstream (App)

```nginx
upstream app {
    server 127.0.0.1:3000;
}
```

### Server Block

```nginx
server {
    listen 80;
    server_name your_domain.com;

    location / {
        proxy_pass http://app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### Enable Gzip

```nginx
gzip on;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml;
```

### Enable Config

```bash
ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled/
nginx -t
systemctl reload nginx
```

### Remove Default Config

```bash
rm /etc/nginx/sites-enabled/default
systemctl reload nginx
```

### Test Routing

```bash
curl http://your_domain.com
```

### Failures

* ❌ 502 Bad Gateway → app not running or wrong port
* ❌ 404 → wrong root/location block
* ❌ Config error → `nginx -t`
* ❌ Permission denied → check SELinux / file perms

---

## 2. Apache Setup

### Install Apache

```bash
apt install apache2
```

### Enable Modules

```bash
a2enmod proxy
a2enmod proxy_http
```

### Virtual Host

```apache
<VirtualHost *:80>
    ServerName your_domain.com

    ProxyPass / http://127.0.0.1:3000/
    ProxyPassReverse / http://127.0.0.1:3000/
</VirtualHost>
```

### Restart

```bash
systemctl restart apache2
```

### Failures

* ❌ Proxy not working → modules not enabled
* ❌ 503 → backend down

---

## 3. OpenLiteSpeed Setup

### Install

```bash
apt install openlitespeed
```

### Configure External App

* Admin panel → External App → Add
* Type: Web Server
* Address: 127.0.0.1:3000

### Setup Listener & Virtual Host

* Map domain to external app

### Restart

```bash
systemctl restart lsws
```

### Failures

* ❌ Cannot connect → wrong app port
* ❌ Config not applied → restart service

---

## 4. Core Reverse Proxy Checks

### Verify App Running

```bash
curl localhost:PORT
```

### Verify Domain Routing

```bash
curl -I http://your_domain.com
```

### Logs

**Nginx**

```bash
/var/log/nginx/access.log
/var/log/nginx/error.log
```

**Apache**

```bash
/var/log/apache2/error.log
```

---

## 5. Final Validation Checklist

* ✅ Reverse proxy installed
* ✅ Upstream correctly configured
* ✅ Domain routes to app
* ✅ Gzip enabled
* ✅ Default configs removed
* ✅ Logs accessible

---

## 6. Emergency Recovery

* Revert config changes
* Restart proxy service
* Check logs immediately

---

This playbook ensures reliable traffic routing from domain → application.
