# Day 4 - SVN Hooks, Backup & Restore (Ubuntu)

This README documents all practical work completed on **Day 4** of APT Training.

---

## **Objective**

Implement SVN hooks (pre-commit, post-commit), configure automated backups using `svnadmin hotcopy`, and understand restoration procedures.

---

## **Repository Path**

Your active SVN repository is located at:

```
/svn/repos/projectA
```

Hooks directory:

```
/svn/repos/projectA/hooks/
```

---

## **1. Pre-Commit Hook**

**Purpose:** Prevent commits without a commit message.

### **Steps:**

1. Navigate to hooks directory:

```
cd /svn/repos/projectA/hooks
```

2. Create the pre-commit hook:

```
nano pre-commit
```

3. Add script:

```
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

4. Make script executable:

```
chmod +x pre-commit
```

5. Test empty commit:

```
svn commit -m "" .
```

**Expected:** Rejects commit.

---

## **2. Post-Commit Hook**

**Purpose:** Log commit information to `/var/log/svn-commits.log`.

### **Steps:**

1. Create hook:

```
nano post-commit
```

2. Add script:

```
#!/bin/bash
REPOS="$1"
REV="$2"
/usr/bin/svnlook author "$REPOS" -r "$REV" >> /var/log/svn-commits.log
/usr/bin/svnlook changed "$REPOS" -r "$REV" >> /var/log/svn-commits.log
echo "-----" >> /var/log/svn-commits.log
exit 0
```

3. Make executable:

```
chmod +x post-commit
```

4. Test by committing a file:

```
svn add test.txt
svn commit -m "Testing post-commit"
cat /var/log/svn-commits.log
```

---

## **3. Convert svnserve to systemd Service**

**Purpose:** Run svnserve properly on boot.

### **Steps:**

1. Stop manually-running svnserve:

```
pkill svnserve
```

2. Create service file:

```
nano /etc/systemd/system/svnserve.service
```

3. Add:

```
[Unit]
Description=Subversion Server
After=network.target

[Service]
Type=forking
User=svn
Group=svn
ExecStart=/usr/bin/svnserve -d -r /svn/repos
ExecStop=/bin/kill -15 $MAINPID
PIDFile=/var/run/svnserve.pid

[Install]
WantedBy=multi-user.target
```

4. Enable + start service:

```
systemctl daemon-reload
systemctl enable --now svnserve
systemctl status svnserve
```

---

## **4. SVN Backup (Hotcopy)**

**Purpose:** Create a safe backup of entire repository.

### **Backup Command:**

```
svnadmin hotcopy /svn/repos/projectA /svn_backups/projectA_backup
```

### **Verify:**

```
ls /svn_backups/projectA_backup
```

---

## **5. Automated Daily Backup (Cron)**

1. Create backup script:

```
nano /usr/local/bin/svn_backup.sh
```

2. Add:

```
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

3. Make executable:

```
chmod +x /usr/local/bin/svn_backup.sh
```

4. Schedule via cron:

```
crontab -e
```

Add:

```
0 2 * * * /usr/local/bin/svn_backup.sh >/dev/null 2>&1
```

---

## **6. Restore Backup**

```
tar -xzf /svn_backups/projectA_<date>.tar.gz -C /tmp
svnadmin hotcopy /tmp/projectA_<date> /svn/repos/projectA_restored
```

Verify:

```
svnlook uuid /svn/repos/projectA_restored
```

---

## **Day 4 Completed Successfully** 🎉

This README represents your completed work for Day 4.
