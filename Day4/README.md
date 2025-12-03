# 📘 Day 4 – SVN Installation, Hooks, Backup & Restore (Docker Edition)

This document contains all practical work completed on **Day 4 of APT Training**, optimized to run **inside a Docker Ubuntu:22.04 container**.
All commands work **without sudo** because you are root inside Docker.

---

# 🐳 1. Start Ubuntu Docker Container

Pull and run Ubuntu:

```bash
docker pull ubuntu:22.04
docker run -it ubuntu:22.04 /bin/bash
```

Inside the container you will be root:

```
root@<container-id>:/#
```

---

# 📦 2. Install SVN & Required Packages

```bash
apt update
apt install -y subversion nano cron
```

---

# 👤 3. Create SVN User & Repository Structure

```bash
adduser --disabled-password --gecos "" svn
mkdir -p /svn/repos
chown -R svn:svn /svn
```

---

# 📁 4. Create SVN Repository (`projectA`)

Inside Docker, use `runuser` instead of `sudo`:

```bash
runuser -u svn -- svnadmin create /svn/repos/projectA
ls /svn/repos/projectA
```

Expected folders: `conf/ db/ hooks/ locks/ format`

---

# 🔧 5. Configure svnserve Access Control

### Edit svnserve.conf:

```bash
nano /svn/repos/projectA/conf/svnserve.conf
```

Add:

```
anon-access = none
auth-access = write
password-db = passwd
```

### Edit passwd file:

```bash
nano /svn/repos/projectA/conf/passwd
```

Add:

```
[users]
user1 = password123
```

---

# 🚀 6. Start svnserve (Foreground-Safe for Docker)

Run as a background process:

```bash
svnserve -d -r /svn/repos &
```

Verify:

```bash
ps aux | grep svnserve
```

---

# 📥 7. Checkout Repository

```bash
svn checkout svn://localhost/projectA /root/projectA_wc
```

---

# 🪝 8. Pre-Commit Hook (Prevent empty commit messages)

```bash
cd /svn/repos/projectA/hooks
nano pre-commit
```

Paste:

```bash
#!/bin/bash
REPOS="$1"
TXN="$2"

/usr/bin/svnlook log -t "$TXN" "$REPOS" | grep "[A-Za-z0-9]" > /dev/null
if [ $? -ne 0 ]; then
    echo "ERROR: Commit message cannot be empty." 1>&2
    exit 1
fi
exit 0
```

Make executable:

```bash
chmod +x pre-commit
```

Test:

```bash
cd /root/projectA_wc
svn commit -m "" .
```

Expected: ❌ error.

---

# 🪝 9. Post-Commit Hook (Log author & changed files)

```bash
cd /svn/repos/projectA/hooks
nano post-commit
```

Paste:

```bash
#!/bin/bash
REPOS="$1"
REV="$2"

LOG="/var/log/svn-commits.log"

mkdir -p /var/log
/usr/bin/svnlook author "$REPOS" -r "$REV" >> "$LOG"
/usr/bin/svnlook changed "$REPOS" -r "$REV" >> "$LOG"
echo "-----" >> "$LOG"
exit 0
```

Make executable:

```bash
chmod +x post-commit
```

Test:

```bash
cd /root/projectA_wc
echo "hello" > test.txt
svn add test.txt
svn commit -m "Testing post-commit"
cat /var/log/svn-commits.log
```

---

# 📦 10. Hotcopy Backup (Instant SVN Backup)

```bash
mkdir -p /svn_backups

svnadmin hotcopy /svn/repos/projectA /svn_backups/projectA_backup

ls /svn_backups/projectA_backup
```

---

# ⏰ 11. Automatic Daily Backup via Cron

### Create backup script:

```bash
nano /usr/local/bin/svn_backup.sh
```

Paste:

```bash
#!/bin/bash
BACKUP_ROOT="/svn_backups"
REPO_PATH="/svn/repos/projectA"
DATE=$(date +%F_%H%M)
DEST="$BACKUP_ROOT/projectA_$DATE"

mkdir -p "$BACKUP_ROOT"
svnadmin hotcopy "$REPO_PATH" "$DEST"
tar -czf "$DEST.tar.gz" -C "$BACKUP_ROOT" "$(basename $DEST)"
rm -rf "$DEST"
```

Make executable:

```bash
chmod +x /usr/local/bin/svn_backup.sh
```

### Add Cron Job:

```bash
crontab -e
```

Add:

```
0 2 * * * /usr/local/bin/svn_backup.sh >/dev/null 2>&1
```

Start cron:

```bash
service cron start
```

---

# 🔄 12. Restore SVN Backup

```bash
tar -xzf /svn_backups/projectA_<date>.tar.gz -C /tmp

svnadmin hotcopy /tmp/projectA_<date> /svn/repos/projectA_restored
```

Verify:

```bash
svnlook uuid /svn/repos/projectA_restored
```

---

# 🎉 Practical Completed

You now have a fully working SVN server inside a Docker container with:

* SVN installation
* User authentication
* Pre-commit and post-commit hooks
* Background svnserve
* Backup & restore (manual + cron automated)

---


