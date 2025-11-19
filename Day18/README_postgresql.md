# 📘 **Day 18 – Backup & Restore (PostgreSQL Only) – README.md**

**APT Admin Training – Term 2**
**Operating System:** Ubuntu
**Tools:** PostgreSQL
**Use Case:** Backup & Restore for Atlassian Tools (JIRA, Crowd, Confluence — PostgreSQL Only)

---

# 📝 **Overview**

This lab teaches how to perform **database backups, file-level backups, and restoration procedures** using PostgreSQL.
These skills are mission-critical for administering Atlassian products deployed on Ubuntu.

This README assumes:

* PostgreSQL is installed
* A database called **jira_db** exists
* Atlassian applications may or may not be installed (lab works regardless)

---

# 🎯 **Objectives**

After completing this lab, you will be able to:

* List existing PostgreSQL databases
* Create backups of PostgreSQL databases
* Restore a full PostgreSQL backup
* Verify backup integrity
* Simulate database failure & recovery
* Automate daily backups using cron
* Manage storage & retention of backup files

---

# 📂 **Directory Setup**

Create backup folder:

```bash
mkdir -p ~/apt-backups/db
mkdir -p ~/apt-backups/app
```

---

# 🟦 **1. Check Existing Databases**

Check PostgreSQL database list:

```bash
sudo -u postgres psql -l
```

Expected default databases:

* postgres
* template0
* template1
* jira_db (or any Atlassian DB you created)

---

# 🟦 **2. Backup PostgreSQL Database (jira_db)**

Backup command:

```bash
sudo -u postgres pg_dump jira_db > ~/apt-backups/db/jira_db_$(date +%F).sql
```

Verify the backup file was created:

```bash
ls -lh ~/apt-backups/db
```

Example result:

```
-rw-r--r-- 1 azureuser azureuser 1.2M jira_db_2025-11-19.sql
```

---

# 🟦 **3. Simulate Database Loss (Optional but Recommended)**

This step helps you practice real disaster recovery.

### Drop the database:

```bash
sudo -u postgres dropdb jira_db
```

Confirm:

```bash
sudo -u postgres psql -l
```

`jira_db` should be missing.

---

# 🟦 **4. Restore PostgreSQL Database from Backup**

### Step 1: Recreate an empty DB:

```bash
sudo -u postgres createdb jira_db
```

### Step 2: Restore backup file:

```bash
sudo -u postgres psql jira_db < ~/apt-backups/db/jira_db_*.sql
```

### Step 3: Verify tables:

```bash
sudo -u postgres psql -c "\dt" jira_db
```

If tables appear → restore succeeded.

---

# 🟦 **5. Verify Backup File Integrity**

### View the first few lines:

```bash
head -n 20 ~/apt-backups/db/jira_db_*.sql
```

### View last few lines:

```bash
tail -n 20 ~/apt-backups/db/jira_db_*.sql
```

If:

* No error messages
* No partial data
* File opens cleanly

✔ Backup is valid.

---

# 🟦 **6. Backup Atlassian Home Directories (If Installed)**

Even if tools aren’t installed yet, these commands demonstrate the technique.

### Example JIRA Home Backup:

```bash
sudo tar -czvf ~/apt-backups/app/jira_home_$(date +%F).tar.gz /var/atlassian/application-data/jira/
```

### Example Crowd Home Backup:

```bash
sudo tar -czvf ~/apt-backups/app/crowd_home_$(date +%F).tar.gz /var/atlassian/application-data/crowd/
```

Verify:

```bash
tar -tzf ~/apt-backups/app/jira_home_*.tar.gz | head
```

---

# 🟦 **7. Restore Atlassian Home Directory Backup**

### Stop application:

```bash
sudo systemctl stop jira
```

### Extract backup:

```bash
sudo tar -xzvf ~/apt-backups/app/jira_home_*.tar.gz -C /
```

### Start application:

```bash
sudo systemctl start jira
```

---

# 🟦 **8. Automate Daily Database Backups**

Create a backup script:

```bash
nano ~/backup_jira.sh
```

Paste:

```bash
#!/bin/bash
BACKUP_DIR="/home/azureuser/apt-backups/db"
DATE=$(date +%F)
sudo -u postgres pg_dump jira_db > $BACKUP_DIR/jira_db_$DATE.sql
find $BACKUP_DIR -type f -mtime +7 -delete
```

Save & exit:

```
CTRL + O → ENTER → CTRL + X
```

Make executable:

```bash
chmod +x ~/backup_jira.sh
```

---

# 🟦 **9. Schedule a Daily Cron Job**

Open cron editor:

```bash
crontab -e
```

Add:

```
0 2 * * * /home/azureuser/backup_jira.sh
```

Backup runs every day at 2 AM.

---

# 📌 **Verification Checklist (PostgreSQL Backup/Restore)**

| Check                              | Status |
| ---------------------------------- | ------ |
| Backup file exists                 | ✔      |
| Backup file readable               | ✔      |
| DB dropped successfully (optional) | ✔      |
| Restore ran without errors         | ✔      |
| Tables restored correctly          | ✔      |
| Cron job scheduled (optional)      | ✔      |
| Old backups auto-deleted           | ✔      |

---

# 🎉 **Day 18 Completed Successfully**

You have now learned:

✔ PostgreSQL backup
✔ PostgreSQL restore
✔ Disaster recovery simulation
✔ File-level (home directory) backup
✔ Backup integrity validation
✔ Automated backups using cron

This knowledge is essential for Atlassian administrators during migrations, upgrades, and DR planning.

---
