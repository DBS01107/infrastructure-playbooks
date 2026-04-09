#  Backup & Recovery Playbook

Production-grade strategy to ensure data safety, system recoverability, and business continuity.

---

## 1. Backup Philosophy (Non-Negotiable)

If you cannot restore, you do not have a backup.

Goals:

* Prevent data loss
* Enable fast recovery
* Minimize downtime

---

## 2. What to Backup

### Critical Data

* Databases (MySQL/PostgreSQL/MongoDB)
* Application data (uploads, user files)
* Environment files (`.env`)

### System Configuration

* Nginx / Apache configs
* Systemd service files
* Firewall rules (optional)

---

## 3. Database Backups

### MySQL

```bash
mysqldump -u root -p db_name > /backups/db_$(date +\%F).sql
```

### PostgreSQL

```bash
pg_dump db_name > /backups/db_$(date +\%F).sql
```

### Automate with Cron

```bash
crontab -e
```

Example (daily at 2 AM):

```bash
0 2 * * * mysqldump -u root -pPASSWORD db_name > /backups/db_$(date +\%F).sql
```

---

## 4. File Backups

### Application + Config

```bash
tar -czf /backups/app_$(date +\%F).tar.gz /var/www
```

---

## 5. Remote Backup (Backup Server)

### Using SCP (Simple)

```bash
scp /backups/* user@backup_server:/backup/
```

### Using Rsync (Recommended)

```bash
rsync -avz /backups/ user@backup_server:/backup/
```

---

### Automate Remote Backup (Cron)

Edit crontab:

```bash
crontab -e
```

Example (daily at 3 AM):

```bash
0 3 * * * rsync -avz /backups/ user@backup_server:/backup/
```

 Ensure SSH key-based authentication is setup (no password prompts)

---

## 6. Backup Storage Strategy

### Local Backup (Fast)

* `/backups` directory

### Remote Backup (Required)

* Another VPS
* Cloud storage (S3, GDrive, etc.)

 Follow **3-2-1 rule**:

* 3 copies
* 2 different media
* 1 offsite

---

## 7. Snapshot Backups (Provider-Level)

Many VPS / cloud providers offer **snapshot backups**.

Use them if:

* You want full system restore
* You are okay with reverting entire VPS state

⚠️ Limitations:

* Not granular (full reset only)
* Cannot selectively restore DB/files

 Recommended approach:

* Use snapshots **+** file/database backups

---

## 8. Restore Procedures

### Restore Database

**MySQL**

```bash
mysql -u root -p db_name < backup.sql
```

**PostgreSQL**

```bash
psql db_name < backup.sql
```

---

### Restore Files

```bash
tar -xzf app_backup.tar.gz -C /
```

---

## 9. Full System Recovery (Worst Case)

1. Provision new VPS
2. Setup base system (use VPS playbook)
3. Restore configs
4. Restore database
5. Deploy application
6. Restart services

---

## 10. Backup Validation (CRITICAL)

Test backups regularly:

```bash
mysql test_db < backup.sql
```

✔ Ensure backup is usable

---

## 11. Common Failures

* ❌ Backup exists but corrupted → no validation
* ❌ Missing files → incomplete backup scope
* ❌ Backup only on same server → useless in failure
* ❌ SSH not configured → remote backup fails
* ❌ Wrong permissions → restore fails

---

## 12. Final Checklist

* ✅ Database backups automated
* ✅ Files backed up
* ✅ Remote backup configured
* ✅ Snapshot backups enabled (if available)
* ✅ Restore tested
* ✅ Backup retention defined

---

## 13. Emergency Recovery Mindset

When system fails:

* Stay calm
* Do not overwrite existing data
* Restore from latest working backup
* Verify system before exposing

---

This playbook ensures your system can survive failure—not just operate normally.
