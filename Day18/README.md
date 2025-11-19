Perfect — here is your **full, detailed, professional README.md for Day 18**, including **PostgreSQL + MySQL/MariaDB backup and restore**, and **Atlassian home directory backup/restore**.

---

# 📘 **Day 18 – Backup & Restore Lab (README.md)**

**APT Admin Training – Day 18**
**Topic:** Backup & Restore for Databases and Atlassian Applications
**OS:** Ubuntu
**Databases Covered:** PostgreSQL + MySQL/MariaDB
**Tools Covered:** JIRA, Crowd, Confluence, Bitbucket (home directories only)

---

# 📝 **Overview**

Day 18 teaches how to perform backup and restore operations for:

* PostgreSQL databases
* MySQL/MariaDB databases
* Atlassian “Home” directories (file-system data)
* Verifying backup integrity
* Practicing disaster recovery and rollback

These skills are critical for:

✔ Migrations
✔ Disaster recovery
✔ Upgrades
✔ Admin-level troubleshooting
✔ Bitbucket/JIRA/Crowd data protection

---

# 🎯 **Objectives**

By the end of this lab, you will be able to:

* Create PostgreSQL backups & restore them
* Create MySQL/MariaDB backups & restore them
* Backup JIRA/Crowd/Confluence home directories
* Restore file-system based application data
* Validate backup file integrity

---

# 📂 **Folder Structure for Backup Files**

Create a backup folder:

```bash
mkdir -p ~/apt-backups/db/postgres
mkdir -p ~/apt-backups/db/mysql
mkdir -p ~/apt-backups/app
```

---

# --------------------------------------------------

# 🟦 **SECTION 1: PostgreSQL BACKUP & RESTORE**

# --------------------------------------------------

Atlassian apps often use PostgreSQL:

* JIRA → `jira_db`
* Crowd → `crowd_db`
* Confluence → `confluence_db`
* Bitbucket (optional)

Check PostgreSQL databases:

```bash
sudo -u postgres psql -l
```

---

## **1.1 PostgreSQL – Backup Database**

Example: Backup JIRA DB

```bash
sudo -u postgres pg_dump jira_db > ~/apt-backups/db/postgres/jira_db_$(date +%F).sql
```

Backup Crowd DB:

```bash
sudo -u postgres pg_dump crowd_db > ~/apt-backups/db/postgres/crowd_db_$(date +%F).sql
```

Verify:

```bash
ls -lh ~/apt-backups/db/postgres
```

---

## **1.2 PostgreSQL – Restore Database**

### Step A: Drop old DB (simulate failure)

```bash
sudo -u postgres dropdb jira_db
```

### Step B: Recreate empty DB

```bash
sudo -u postgres createdb -O postgres jira_db
```

### Step C: Restore from backup

```bash
sudo -u postgres psql jira_db < ~/apt-backups/db/postgres/jira_db_*.sql
```

---

# --------------------------------------------------

# 🟦 **SECTION 2: MySQL/MariaDB BACKUP & RESTORE**

# --------------------------------------------------

This is required for **Bitbucket**, which supports MySQL/MariaDB.

Check installed databases:

```bash
mysql -u root -p -e "SHOW DATABASES;"
```

Your Bitbucket setup should contain:

```
bitbucket_db
```

---

## **2.1 MySQL/MariaDB – Backup Database**

Backup Bitbucket DB:

```bash
mysqldump -u root -p bitbucket_db > ~/apt-backups/db/mysql/bitbucket_db_$(date +%F).sql
```

Verify:

```bash
ls -lh ~/apt-backups/db/mysql
```

---

## **2.2 MySQL – Restore Database**

### Step A: Drop database

```bash
mysql -u root -p -e "DROP DATABASE bitbucket_db;"
```

### Step B: Recreate database

```bash
mysql -u root -p -e "CREATE DATABASE bitbucket_db CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;"
```

### Step C: Restore from backup

```bash
mysql -u root -p bitbucket_db < ~/apt-backups/db/mysql/bitbucket_db_*.sql
```

---

# --------------------------------------------------

# 🟦 **SECTION 3: Backup Atlassian Home Directories**

# --------------------------------------------------

These contain:

* Attachments
* Logs
* Plugins
* Indexes
* Cache
* Clustering data

Default locations (if installed):

| Tool       | Default Home Directory                      |
| ---------- | ------------------------------------------- |
| JIRA       | /var/atlassian/application-data/jira/       |
| Confluence | /var/atlassian/application-data/confluence/ |
| Crowd      | /var/atlassian/application-data/crowd/      |
| Bitbucket  | /var/atlassian/application-data/bitbucket/  |

---

## **3.1 Backup JIRA Home Directory**

```bash
sudo tar -czvf ~/apt-backups/app/jira_home_$(date +%F).tar.gz /var/atlassian/application-data/jira/
```

## **3.2 Backup Crowd Home Directory**

```bash
sudo tar -czvf ~/apt-backups/app/crowd_home_$(date +%F).tar.gz /var/atlassian/application-data/crowd/
```

## **3.3 Backup Confluence Home Directory**

```bash
sudo tar -czvf ~/apt-backups/app/confluence_home_$(date +%F).tar.gz /var/atlassian/application-data/confluence/
```

## **3.4 Backup Bitbucket Home Directory**

```bash
sudo tar -czvf ~/apt-backups/app/bitbucket_home_$(date +%F).tar.gz /var/atlassian/application-data/bitbucket/
```

Verify:

```bash
ls -lh ~/apt-backups/app
```

---

# --------------------------------------------------

# 🟦 **SECTION 4: Restore Atlassian Home Directories**

# --------------------------------------------------

### Example: Restore JIRA Home

### Step 1 — Stop service

```bash
sudo systemctl stop jira
```

### Step 2 — Extract backup

```bash
sudo tar -xzvf ~/apt-backups/app/jira_home_*.tar.gz -C /
```

### Step 3 — Start service

```bash
sudo systemctl start jira
```

Same logic applies for Crowd, Confluence, Bitbucket.

---

# --------------------------------------------------

# 🟦 **SECTION 5: Verify Backup Integrity**

# --------------------------------------------------

### PostgreSQL backup check:

```bash
head ~/apt-backups/db/postgres/jira_db_*.sql
```

### MySQL backup check:

```bash
head ~/apt-backups/db/mysql/bitbucket_db_*.sql
```

### Tar archive integrity check:

```bash
tar -tzf ~/apt-backups/app/jira_home_*.tar.gz
```

If it lists files → valid backup.

---

# --------------------------------------------------

# 🟢 **Summary: What You Learned in Day 18**

# --------------------------------------------------

✔ How to backup & restore PostgreSQL databases
✔ How to backup & restore MySQL/MariaDB databases
✔ Creating and verifying Bitbucket database
✔ How to backup Atlassian HOME directories
✔ How to restore file-system backups
✔ How to validate backup integrity
✔ Disaster recovery basics for Atlassian tools

---
