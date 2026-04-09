#  Deployment Playbook

Production-ready, failure-aware deployment guide across common stacks.

---

## 1. Node.js Deployment

### Install Runtime

```bash
apt install nodejs npm
node -v
```

### Setup Application

```bash
npm install
```

### Environment Variables

* Create `.env`
* Use `dotenv` or systemd/PM2 env config

---

### Option A: PM2 (Quick + Developer Friendly)

```bash
npm install -g pm2
pm2 start app.js --name app
pm2 save
pm2 startup
```

**Verify:**

```bash
pm2 status
pm2 logs
```

**Failures:**

* ❌ App restarts repeatedly → check logs (`pm2 logs`)
* ❌ Env vars not loaded → use `--env` or ecosystem.config.js

---

### Option B: Systemd (Production Preferred)

```ini
[Unit]
Description=Node App
After=network.target

[Service]
User=www-data
WorkingDirectory=/var/www/app
ExecStart=/usr/bin/node app.js
Restart=always
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable app
sudo systemctl start app
```

### Verify

```bash
curl localhost:PORT
```

### Failures

* ❌ Port in use → `lsof -i :PORT`
* ❌ Permission denied → check `User` + directory perms
* ❌ App crash → `journalctl -u app`

---

### Install Runtime

```bash
apt install nodejs npm
node -v
```

### Setup Application

```bash
npm install
```

### Environment Variables

* Create `.env`
* Use `dotenv` or systemd env

### Systemd Service (Recommended over PM2 for prod)

```ini
[Unit]
Description=Node App
After=network.target

[Service]
User=www-data
WorkingDirectory=/var/www/app
ExecStart=/usr/bin/node app.js
Restart=always
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable app
sudo systemctl start app
```

### Verify

```bash
curl localhost:PORT
```

### Failures

* ❌ Port in use → `lsof -i :PORT`
* ❌ Permission denied → check `User` + directory perms
* ❌ App crash → `journalctl -u app`

---

## 2. Django / Python Deployment

### Install Runtime

```bash
apt install python3 python3-venv python3-pip
```

### Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Run App (Gunicorn)

```bash
pip install gunicorn
gunicorn app.wsgi:application
```

### Systemd Service

```ini
[Unit]
Description=Django App
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/var/www/app
ExecStart=/var/www/app/venv/bin/gunicorn --workers 3 --bind unix:app.sock app.wsgi:application
Restart=always
Environment="PATH=/var/www/app/venv/bin"

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable app
sudo systemctl start app
```

### Failures

* ❌ Module errors → ensure venv path correct
* ❌ Socket not created → check working dir
* ❌ Static issues → `collectstatic`

### Logs

```bash
journalctl -u app
```

---

## 3. Java (Spring Boot / JAR)

### Install Runtime

```bash
apt install openjdk-17-jdk
java -version
```

### Systemd Service

```ini
[Unit]
Description=Java App
After=network.target

[Service]
User=www-data
WorkingDirectory=/var/www/app
ExecStart=/usr/bin/java -jar app.jar
Restart=always
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable app
sudo systemctl start app
```

### Failures

* ❌ Port conflict → change `server.port`
* ❌ Memory issue → add JVM flags
* ❌ Crash → check logs

### Logs

```bash
journalctl -u app
```

---

## 4. Docker-Based Deployment

### Install Docker

```bash
apt install docker.io
docker --version
```

### Build & Run

```bash
docker build -t app .
docker run -d -p 80:3000 --name app --restart unless-stopped app
```

### Docker Compose

```bash
docker-compose up -d
```

### Failures

* ❌ Container exits → `docker logs app`
* ❌ Port issue → check binding
* ❌ Build fails → inspect Dockerfile

### Logs

```bash
docker logs -f app
```

---

## 5. Core Deployment Checks

### App Health

```bash
curl localhost:PORT
```

### Auto Restart

* systemd / Docker restart policies

### Logging

* journalctl / docker logs

---

## 6. Final Validation Checklist

* ✅ App runs locally
* ✅ Env variables configured
* ✅ Service enabled & running
* ✅ Auto-restart enabled
* ✅ Logs accessible
* ✅ No port conflicts

---

## 7. Emergency Recovery

* Restart service: `systemctl restart app`
* Check logs immediately
* Rollback if needed

---

This playbook is designed to be repeatable, minimal, and production-focused.
