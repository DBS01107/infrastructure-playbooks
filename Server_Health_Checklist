# Production VPS Operational Checklist
### Platform Engineering & Infrastructure Security

**Purpose:**  
Ensure system health, security posture, and service availability on every administrative login.

**Operational Rule:**  
Use this checklist every time you SSH into production.

---

# 1. System Overview

## 1.1 Uptime and Load

Confirm the system is stable and not under abnormal load.

**Command**
```
uptime
```


**Checks**

- system uptime looks normal
- load average is reasonable relative to CPU cores
- no unexplained spikes

---

## 1.2 CPU and Memory Usage

**Command**
```
htop
```


**Checks**

- CPU not saturated
- memory not exhausted
- swap not heavily used
- no suspicious processes

---

## 1.3 Memory Summary

**Command**
```
free -h
```

**Checks**

- available memory present
- swap not constantly active

---

# 2. Service Health

## 2.1 Failed Services

**Command**
```
systemctl --failed
```


**Checks**

- no failed services listed
- investigate immediately if any appear

---

## 2.2 Critical Application Services

Verify production applications are running.

**Command**
```
systemctl status <service-name>
```


**Checks**

- Active: running
- no restart loops
- no recent errors

---

## 2.3 Apache / Reverse Proxy

**Command**
```
systemctl status apache2
```


**Checks**

- service active
- no crash loops
- ports responding

---

# 3. Network Exposure

## 3.1 Open Ports

**Command**
```
ss -tulnp
```

**Checks**

Expected ports only


Verify:

- backend services are not exposed externally
- no unknown listening ports

---

## 3.2 Reverse Proxy Routing

Confirm backend services are reachable via proxy.

**Checks**

- domains resolving correctly
- Apache routing functioning
- backend services responding

---

# 4. SSH and Access Security

## 4.1 Active Login Sessions

**Command**
```
who
#or 
w
```

**Checks**

- only expected users logged in
- no unknown IP addresses

---

## 4.2 Historical Logins

**Command**
```
last -a
```

**Checks**

- unexpected login locations
- unusual login times
- repeated root logins

---

## 4.3 Authentication Logs

**Command**
```
tail -n 20 /var/log/auth.log
```

**Checks**

- repeated login failures
- privilege escalations
- unusual authentication activity

---

# 5. Intrusion Prevention

## 5.1 Fail2Ban Status

**Command**
```
fail2ban-client status
```

**Checks**

- sshd jail active
- banned IP count increasing normally

**Detailed Check**
```
fail2ban-client status sshd
```

---

# 6. Disk and Storage

## 6.1 Disk Usage

**Command**
```
df -h
```


**Checks**

- partitions below 85–90% usage
- investigate if nearing capacity


---

## 6.2 Log Size Growth

**Command**
```du -sh /var/log/*```

**Checks**

- no abnormal log growth
- log rotation functioning

---

# 7. Application Processes

## 7.1 Java Services

**Command**
```ps aux | grep java```

**Checks**

- expected Java services only
- no rogue processes

---

## 7.2 Node Services

**Command**
```
ps aux | grep node
```

**Checks**

- only expected node applications running

---

# 8. Apache Logs

## 8.1 Error Logs

**Command**
```
tail -n 20 /var/log/apache2/error.log
```

**Checks**

- backend proxy failures
- SSL issues
- application crashes

---

## 8.2 Access Logs (Optional)

**Command**
```
tail -n 20 /var/log/apache2/access.log
```

**Checks**

- unusual request spikes
- scanning patterns

---

# 9. Configuration Integrity

## 9.1 Apache Configuration

Verify reverse proxy configurations exist and are correct.

**Location**
```
/etc/apache2/sites-available
```

**Checks**

- expected domain configurations present
- no accidental changes

---

## 9.2 Systemd Service Files

**Location**
```
/etc/systemd/system
```

**Checks**

- services configured correctly
- restart policies active

---

# 10. Security Configuration

## 10.1 SSH Hardening

Verify the following settings:
- PasswordAuthentication no
- PermitRootLogin no


---

## 10.2 Firewall Status

**Command**
```
ufw status
```

**Checks**

Only required ports allowed.

- 80
- 443
- 22 

or 

- Apache/nignx full
- 22


---

# 11. Quick System Snapshot (Fast Check)

Senior engineers often run a fast summary.

**Command**
```
uptime && df -h && free -h && systemctl --failed
```

**Purpose**

Quick overview of system health.

---

# 12. Optional Operational Checks

## 12.1 Recent Commands

**Command**
```
history | tail
```

**Checks**

- unexpected commands
- accidental configuration changes

---

## 12.2 Recent Logins

**Command**
```
lastlog
```

**Checks**

- new accounts accessing system

---

# Operational Rule

Every production login should answer these questions:

- Is the system healthy?
- Are services running?
- Is anything exposed unexpectedly?
- Is anyone logged in who shouldn't be?
- Is disk space safe?

If all answers are yes, the system is healthy.
