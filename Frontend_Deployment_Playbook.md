# Frontend Deployment Playbook (Web Server + Reverse Proxy)

Production-ready guide for deploying frontend applications on VPS, including reverse proxy configuration for both frontend and backend in a single configuration.

In this setup, Apache/Nginx/OpenLiteSpeed act as a web server for frontend assets and a reverse proxy for backend APIs, unified under a single configuration.

---

## ⚠️ When NOT to Use VPS

If you have access to modern hosting platforms:

* Vercel
* Netlify
* Cloudflare Pages
* specialized Hostinger/GoDaddy Web-Hosting plans
 Prefer them for frontend.

Use VPS only when:

* Full infrastructure control is required
* Backend + frontend tightly coupled
* Internal/private deployments

---

## 1. Build Frontend

### Node-based Frontend (React / Vue / Angular)

```bash
npm install
npm run build
```

### Output

* `dist/` (Vite)
* `build/` (CRA)

---

## 2. Deploy Build to Server

```bash
cp -r dist /var/www/frontend
```

Set permissions:

```bash
chown -R www-data:www-data /var/www/frontend
```

---

## 3. Nginx Configuration (Frontend + Backend)

```nginx
server {
    listen 80;
    server_name your_domain.com;

    root /var/www/frontend;
    index index.html;

    location / {
        try_files $uri /index.html;
    }

    location /api {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

---

## 4. Apache Configuration (Frontend + Backend)

```apache
<VirtualHost *:80>
    ServerName your_domain.com

    DocumentRoot /var/www/frontend

    <Directory /var/www/frontend>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ /index.html [L]

    ProxyPass /api http://127.0.0.1:3000/
    ProxyPassReverse /api http://127.0.0.1:3000/
</VirtualHost>
```

Enable modules:

```bash
a2enmod proxy
a2enmod proxy_http
a2enmod rewrite
```

---

## 5. OpenLiteSpeed (LSWS)

### Steps

* Admin Panel → Virtual Host
* Set Document Root: `/var/www/frontend`
* Enable Rewrite

### Rewrite Rules (SPA)

```txt
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)$ /index.html [L]
```

### External App (Backend)

* Address: 127.0.0.1:3000

### Context Mapping

* URI: /api → backend app

---

## 6. HTTPS

Use SSL/TLS playbook (Certbot or Cloudflare)

---

## 7. Common Failures

* ❌ Blank page → wrong build directory
* ❌ 404 on refresh → missing SPA fallback
* ❌ API not working → proxy path mismatch
* ❌ CORS issues → backend misconfiguration

---

## 8. Validation

```bash
curl http://your_domain.com
```

```bash
curl http://your_domain.com/api
```

---

## 9. Final Checklist

* ✅ Frontend build deployed
* ✅ Web server serving static files
* ✅ Reverse proxy routing API correctly
* ✅ No refresh 404 issues
* ✅ Permissions correct

---

## 10. Emergency Fix

* Rebuild frontend
* Restart web server
* Check logs

---

This playbook unifies frontend hosting and backend routing under a single reverse proxy configuration.
