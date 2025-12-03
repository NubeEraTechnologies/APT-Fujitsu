
# 📘 **DAY 2 – SVN Administration (Ubuntu) – README.md**

## **Overview**

Day 2 focuses on installing and configuring **SVN (Subversion)** on **Ubuntu**, creating your first repository, configuring users and permissions, performing basic operations (checkout, add, commit), and setting up the standard repository layout with branching and merging.

---

# 🧰 **Prerequisites**

* Ubuntu 20.04 / 22.04 (fresh or existing)
* Sudo-enabled user
* Internet connection

---

# 🚀 **1. Install SVN on Ubuntu**

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y subversion
svn --version
```

---

# 📁 **2. Create SVN Repository Directory**

```bash
sudo mkdir -p /svn/repos
sudo svnadmin create /svn/repos/projectA
sudo chmod -R 755 /svn/repos
```

Repository structure will contain:

```
conf/ db/ hooks/ locks/ README.txt
```

---

# 🔐 **3. Configure Users**

Edit the passwd file:

```bash
sudo nano /svn/repos/projectA/conf/passwd
```

Add:

```
[users]
admin = admin123
developer1 = dev123
developer2 = dev456
```

---

# 🔏 **4. Configure Permissions (authz)**

```bash
sudo nano /svn/repos/projectA/conf/authz
```

Use:

```
[groups]
devs = developer1, developer2

[/]
admin = rw
@devs = rw
* = r
```

---

# ⚙️ **5. Configure svnserve.conf**

```bash
sudo nano /svn/repos/projectA/conf/svnserve.conf
```

Uncomment/add:

```
[general]
anon-access = none
auth-access = write
password-db = passwd
authz-db = authz
realm = ProjectA Repository
```

---

# ▶️ **6. Start SVN Server**

```bash
sudo svnserve -d -r /svn/repos
```

Check:

```bash
ps aux | grep svnserve
```

---

# 📥 **7. Checkout Repository**

```bash
mkdir ~/svnworkingcopy
cd ~/svnworkingcopy
svn checkout svn://localhost/projectA --username admin
```

Enter password: `admin123`
When asked to save unencrypted password → **yes**.

---

# 📄 **8. Add Your First File**

```bash
cd ~/svnworkingcopy/projectA
echo "Welcome to SVN Project A" > readme.txt
svn add readme.txt
svn commit -m "Initial commit: added readme file"
```

Verify from server:

```bash
svn ls svn://localhost/projectA
```

---

# 🏗 **9. Create Standard SVN Layout**

Inside working copy:

```bash
cd ~/svnworkingcopy/projectA
mkdir trunk branches tags
mv readme.txt trunk/
svn add trunk branches tags
svn commit -m "Added standard project structure"
```

Verify:

```bash
svn ls svn://localhost/projectA
svn ls svn://localhost/projectA/trunk
```

---

# 🌿 **10. Create a Branch**

```bash
svn copy svn://localhost/projectA/trunk \
         svn://localhost/projectA/branches/feature-readme-update \
         -m "Created branch: feature-readme-update"
```

List branches:

```bash
svn ls svn://localhost/projectA/branches
```

---

# 🔄 **11. Switch to Branch**

```bash
cd ~/projectA-trunk
svn switch svn://localhost/projectA/branches/feature-readme-update
```

---

# ✏️ **12. Modify File in Branch**

```bash
echo "This line was added in the feature-readme-update branch." >> readme.txt
svn status
svn commit -m "Updated readme in branch"
```

---

# 🔁 **13. Merge Branch → Trunk**

Switch back:

```bash
svn switch svn://localhost/projectA/trunk
```

Merge changes:

```bash
svn merge svn://localhost/projectA/branches/feature-readme-update
```

Commit merge:

```bash
svn commit -m "Merged feature-readme-update branch into trunk"
```

---

# ✔️ **14. Verify Final Result**

```bash
svn cat svn://localhost/projectA/trunk/readme.txt
```

You should see the updated lines from the branch.

---

# 🎉 **Day 2 Completed**

You have successfully learned:

* SVN installation
* Repository creation
* User authentication
* Permissions
* Checkout, add, commit
* Standard SVN structure
* Branching & merging

---
