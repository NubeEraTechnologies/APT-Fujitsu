# Day 12 – Crowd 7.x Installation & Internal Directory Setup (Ubuntu)

## ✅ Overview

This lab covers:

* Installing Java 11
* Extracting Crowd 7.x correctly
* Configuring Crowd Home (CROWD_HOME)
* Starting Crowd
* Completing the Setup Wizard
* Creating an Internal Directory
* Adding Users & Groups
* Adding an Application
* Testing authentication

---

## ⭐ Prerequisites

* Ubuntu system (18.04 / 20.04 / 22.04)
* sudo privileges
* Crowd 7.x tar.gz archive downloaded
* Basic knowledge of Linux commands

---
## 📥 Crowd 7.x Download Links

Crowd official download archives:

* Latest Crowd 7.x: [https://www.atlassian.com/software/crowd/download-archives](https://www.atlassian.com/software/crowd/download-archives)
* Direct product page: [https://www.atlassian.com/software/crowd](https://www.atlassian.com/software/crowd)


## ⭐ Step 1 — Install Java 11

Crowd 7.x requires Java 11.

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y openjdk-11-jdk
java -version
```

Expected output:

```
openjdk version "11.x.x"
```

---

## ⭐ Step 2 — Prepare Crowd Directories

Crowd uses separate folders for application files and home directory.

```bash
sudo mkdir -p /var/atlassian/crowd
sudo chmod -R 777 /var/atlassian/crowd
```

---

## ⭐ Step 3 — Extract Crowd 7.x Correctly

Move your downloaded tar.gz file into `/opt/crowd`.

Example:

```bash
cd /opt/crowd
sudo tar -xvf atlassian-crowd-7*.tar.gz
```

This creates a directory like:

```
atlassian-crowd-7.1.0
```

Rename it to the proper application folder:

```bash
sudo mv /opt/crowd/atlassian-crowd-7.1.0 /opt/crowd/crowd
```

Verify structure:

```bash
ls /opt/crowd/crowd
```

You should see:

```
apache-tomcat/
crowd-openid-client/
crowd-rest/
start_crowd.sh
stop_crowd.sh
```

---

## ⭐ Step 4 — Configure Crowd Home (Correct for Crowd 7.x)

Crowd 7.x uses **Tomcat's setenv.sh**, not `crowd-init.properties`.

Create the file:

```bash
sudo nano /opt/crowd/crowd/apache-tomcat/bin/setenv.sh
```

Add:

```
export CROWD_HOME=/var/atlassian/crowd
```

Make executable:

```bash
sudo chmod +x /opt/crowd/crowd/apache-tomcat/bin/setenv.sh
```

---

## ⭐ Step 5 — Start Crowd

```bash
cd /opt/crowd/crowd
./start_crowd.sh
```

Check logs:

```bash
tail -f /opt/crowd/crowd/apache-tomcat/logs/catalina.out
```

Look for:

```
Server startup in XXXX ms
```

---

## ⭐ Step 6 — Access Crowd Web Interface

Open in browser:

```
http://<SERVER-IP>:8095/crowd
```

You should see the **Crowd Setup Wizard**.

Choose:

* **Use Internal Database** (best for training)
* Create admin user (example: admin / admin123)

---

## ⭐ Step 7 — Create Internal Directory

Inside Crowd UI:

```
Crowd Console → Directories → Add Directory → Internal Directory
```

Name it:

```
APT-Internal
```

Enable:

* Active

Save.

---

## ⭐ Step 8 — Create Users & Groups

### Create Groups

```
Directories → APT-Internal → Groups → Add Group
```

Create:

* admins
* developers
* testers

### Create Users

```
Directories → APT-Internal → Users → Add User
```

Examples:

| Username | Password | Group      |
| -------- | -------- | ---------- |
| admin    | admin123 | admins     |
| dev1     | pass123  | developers |
| tester1  | pass123  | testers    |

---

## ⭐ Step 9 — Add an Application (JIRA / Confluence / Any Tool)

Even if the tool isn’t installed, create the application entry.

```
Applications → Add Application
```

Choose:

```
Generic Application
```

Fill:

```
Application Name: JIRA-App
Password: jira_app_pass
IP Address: 0.0.0.0/0
```

Enable:

* Use for Authentication
* Use for Authorization

Save.

---

## ⭐ Step 10 — Link Directory to Application

```
Applications → JIRA-App → Directories → Add Directory
```

Choose:

```
APT-Internal
```

Enable:

* Allow All Groups
* Allow All Users

Save.

---

## ⭐ Step 11 — Test Authentication

In Crowd UI:

```
Applications → JIRA-App → Permissions → Test Authentication
```

Test with user:

```
Username: dev1
Password: pass123
```

Should return:

```
Authentication successful
```

---

## 🎉 Completion Checklist (Day 12 Success)

You have completed Day 12 successfully if:

* [ ] Crowd runs at `http://IP:8095/crowd`
* [ ] `CROWD_HOME` set correctly in `setenv.sh`
* [ ] Internal directory created
* [ ] Users and groups created
* [ ] Application created (JIRA-App)
* [ ] Directory linked to application
* [ ] Authentication test successful

---


