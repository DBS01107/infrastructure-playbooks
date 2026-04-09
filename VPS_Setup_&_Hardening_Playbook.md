#  VPS Setup & Hardening Playbook

A production-grade, failure-aware checklist for secure VPS provisioning.

---

## 1. Access & Base Setup

### 1.1 Verify SSH Login

**Steps:**

```bash
ssh root@your_server_ip
```

**Expected:** Successful login

**Common Failures & Fixes:**

* ❌ Connection refused

  * Check if VPS is running
  * Verify correct IP
  * Check provider firewall (cloud panel)
* ❌ Permission denied

  * Ensure correct password or SSH key
  * Check `~/.ssh/authorized_keys`

---

### 1.2 Create Non-root Sudo User

**Steps:**

```bash
adduser userA
usermod -aG sudo userA
```

**Verify:**

```bash
su - userA
sudo whoami
```

**Expected:** `root`

**Failures:**

* ❌ usermod fails → ensure running as root
* ❌ sudo not found → install:

```bash
apt install sudo
```

---

### 1.3 Disable Root Login

**File:** `/etc/ssh/sshd_config`

```bash
PermitRootLogin no
```

**Apply:**

```bash
systemctl restart ssh
```

⚠️ Test new user login before disabling root.

**Failures:**

* ❌ Locked out → Use VPS console to revert config

---

### 1.4 Change SSH Port

```bash
Port 2222
```

**Restart SSH:**

```bash
systemctl restart ssh
```

**Verify:**

```bash
ssh -p 2222 userA@ip
```

**Failures:**

* ❌ Cannot connect → open port in firewall/cloud panel

---

### 1.5 Setup SSH Key Authentication

**Local:**

```bash
ssh-keygen
ssh-copy-id -p 2222 userA@ip
```

**Disable password login:**

```bash
PasswordAuthentication no
```

**Failures:**

* ❌ Permission denied (publickey)

  * Check permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## 2. Firewall & Network

### 2.1 Enable UFW

```bash
apt install ufw
ufw enable
```

**Failures:**

* ❌ SSH disconnect → forgot to allow SSH first

---

### 2.2 Allow Required Ports

```bash
ufw allow 2222/tcp
ufw allow 80
ufw allow 443
```

**Check:**

```bash
ufw status
```

---

### 2.3 Default Policies

```bash
ufw default deny incoming
ufw default allow outgoing
```

---

### 2.4 Verify Open Ports

```bash
ss -tulnp
```

**Failures:**

* ❌ Unexpected open ports

  * Check running services:

```bash
systemctl list-units --type=service
```

---

## 3. Security

### 3.1 Install Fail2Ban

```bash
apt install fail2ban
```

**Basic Config:**

```bash
cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Enable SSH jail:

```ini
[sshd]
enabled = true
port = 2222
```

**Restart:**

```bash
systemctl restart fail2ban
```

**Failures:**

* ❌ Jail not active → check logs:

```bash
fail2ban-client status
journalctl -u fail2ban
```

---

### 3.2 Unattended Upgrades

```bash
apt install unattended-upgrades
```

```bash
dpkg-reconfigure unattended-upgrades
```

**Failures:**

* ❌ Not updating → check:

```bash
cat /var/log/unattended-upgrades/unattended-upgrades.log
```

---

### 3.3 Remove Unused Packages

```bash
apt autoremove -y
apt autoclean
```

---

### 3.4 Reduce Attack Surface

```bash
ss -tulnp
```

Stop unwanted services:

```bash
systemctl disable --now service_name
```

---

## 4. System Configuration

### 4.1 Set Timezone

```bash
timedatectl set-timezone Asia/Kolkata
```

**Verify:**

```bash
timedatectl
```

---

### 4.2 Setup Logging

Check logs:

```bash
journalctl -xe
```

Install logrotate (usually default):

```bash
apt install logrotate
```

---

### 4.3 Disk Usage

```bash
df -h
```

**Failures:**

* ❌ Disk full → clean:

```bash
apt clean
journalctl --vacuum-time=7d
```

---

### 4.4 RAM Usage

```bash
free -h
```

**Failures:**

* ❌ High usage

  * Check processes:

```bash
top
htop
```

---

## 5. Final Validation Checklist

* ✅ SSH key login works
* ✅ Root login disabled
* ✅ Firewall active with minimal ports
* ✅ Fail2Ban active
* ✅ Auto updates enabled
* ✅ No unnecessary services running
* ✅ Logs working
* ✅ Disk & RAM healthy

---

## 6. Emergency Recovery

If locked out:

* Use VPS provider console
* Re-enable root login temporarily
* Fix SSH config

---

This playbook is designed to be repeatable, failure-aware, and production-ready.
