# Day 3 Lab Guide – SVN Advanced (Branches, Tags, Permissions)

This README.md contains **all steps performed in Day 3**, starting completely **from scratch** (even if Day 1 and Day 2 were not done).

---

## 📝 Overview
In this lab, you:
- Installed SVN (Subversion)
- Created a repository (`projectA`)
- Created folder structure (trunk/branches/tags)
- Checked out working copy
- Created a feature branch
- Switched between trunk and branch
- Merged branch into trunk
- Created a tag
- Configured users and permissions (authz)

---

# ✅ Part 1 — Install SVN From Scratch

### Update system
```bash
sudo apt update && sudo apt upgrade -y
```

### Install SVN
```bash
sudo apt install -y subversion
svn --version
```

---

# ✅ Part 2 — Create SVN Root Directory
```bash
sudo mkdir -p /svn/repos
sudo chmod -R 777 /svn/repos
```

---

# ✅ Part 3 — Create Repository
```bash
sudo svnadmin create /svn/repos/projectA
ls -la /svn/repos/projectA
```

---

# ✅ Part 4 — Start SVN Server
```bash
sudo svnserve -d -r /svn/repos
ps aux | grep svnserve
```

---

# ✅ Part 5 — Configure Users (passwd file)
Edit password file:
```bash
sudo nano /svn/repos/projectA/conf/passwd
```
Add:
```
[users]
admin = admin123
developer = dev123
tester = test123
```

---

# ✅ Part 6 — Configure Permissions (authz file)
```bash
sudo nano /svn/repos/projectA/conf/authz
```
Add:
```
[groups]
team = admin, developer, tester

[/]
admin = rw
developer = rw
tester = r
```

---

# ✅ Part 7 — Create Standard SVN Folder Structure

### Create local structure
```bash
mkdir ~/svnworkingcopy
cd ~/svnworkingcopy
mkdir trunk branches tags
sudo nano /svn/repos/projectA/conf/svnserve.conf
```

Copy and paste this inside:

```
[general]
anon-access = none
auth-access = write
password-db = passwd
authz-db = authz
```

Save: **CTRL + O**, ENTER
Exit: **CTRL + X**

---

# After editing, restart svnserve

```bash
sudo pkill svnserve
sudo svnserve -d -r /svn/repos
```

---

# Clear cached credentials

```bash
rm -rf ~/.subversion/auth
```

### Import into repository
```bash
svn import . svn://localhost/projectA -m "Initial folder structure"
```

---

# ✅ Part 8 — Checkout Trunk Correctly
> (Fix for the common ancestry error)

```bash
rm -rf ~/projectA_wc
svn checkout svn://localhost/projectA/trunk ~/projectA_wc --username admin
cd ~/projectA_wc
svn info
```

---

# ✅ Part 9 — Add Sample File to Trunk
```bash
cd ~/projectA_wc
echo "Hello SVN!" > readme.txt
svn add readme.txt
svn commit -m "Added readme file"
```

---

# ✅ Part 10 — Create Feature Branch
```bash
svn copy svn://localhost/projectA/trunk \
         svn://localhost/projectA/branches/feature-login \
         -m "Created feature-login branch"
```

---

# ✅ Part 11 — Switch Working Copy to Branch
```bash
cd ~/projectA_wc
svn switch svn://localhost/projectA/branches/feature-login
svn info
```

---

# ✅ Part 12 — Modify File in Branch
```bash
echo "Login form added" >> readme.txt
svn commit -m "Updated login form in feature branch"
```

---

# ✅ Part 13 — Switch Back to Trunk
```bash
svn switch svn://localhost/projectA/trunk
```

### Merge branch into trunk
```bash
svn merge svn://localhost/projectA/branches/feature-login
svn commit -m "Merged feature-login branch into trunk"
```

---

# ✅ Part 14 — Create a Tag
```bash
svn copy svn://localhost/projectA/trunk \
         svn://localhost/projectA/tags/v1.0 \
         -m "Release version 1.0"
```

---

# ✅ Part 15 — Restrict Branch Access Example
```bash
sudo nano /svn/repos/projectA/conf/authz
```
Add:
```
[branches]
tester = 
developer = rw
admin = rw
```

Restart svnserve:
```bash
sudo pkill svnserve
sudo svnserve -d -r /svn/repos
```

---

# 🎯 Final Outputs for Day 3
You successfully:
- Installed SVN
- Created `projectA` repository
- Setup users & permissions
- Imported trunk/branches/tags
- Created a branch and merged it
- Created tag `v1.0`
- Tested path-based access control

This file represents the **complete Day 3 lab documentation**.

---

