# Day 10 – Jira Administration 


## 1. Prerequisites

* Ubuntu 20.04 / 22.04 server
* Java 17 or 21 (Zulu OpenJDK recommended)
* PostgreSQL installed and configured
* Jira installer uploaded to server
* sudo privileges

---

## 2. Install Java 21

```
sudo apt update
sudo apt install -y wget gnupg
wget https://repos.azul.com/azul-repo.key
sudo apt-key add azul-repo.key

echo "deb http://repos.azul.com/zulu/deb stable main" | sudo tee /etc/apt/sources.list.d/zulu.list
sudo apt update
sudo apt install -y zulu21-jdk

java -version
```

Expected output: Java 21.x

---

## 3. Create Jira User & Directories

```
sudo adduser jira
sudo usermod -aG sudo jira
sudo mkdir -p /opt/atlassian/jira
sudo mkdir -p /var/atlassian/application-data/jira
sudo chmod -R 777 /opt/atlassian
sudo chmod -R 777 /var/atlassian/application-data
```

---

## 4. Install PostgreSQL

```
sudo apt install -y postgresql postgresql-contrib
```

Create DB + user:

```
sudo -u postgres createuser --pwprompt jira_user
sudo -u postgres createdb -O jira_user jira_db
sudo -u postgres psql -c "ALTER DATABASE jira_db SET client_encoding TO 'UTF8';"
```

---

## 5. Install Jira Software 10.x

Upload installer to `/opt/` then run:

```
cd /opt
sudo chmod +x atlassian-jira-software-10.3.13-x64.bin
sudo ./atlassian-jira-software-10.3.13-x64.bin
```

Installer selections:

* Install Jira → Yes
* Installation directory → /opt/atlassian/jira-10.x.x
* Home directory → /var/atlassian/application-data/jira
* Use bundled JRE → **No**, select Java 17 or 21
* Run as service → Yes

Start Jira:

```
sudo systemctl start jira
sudo systemctl status jira
```

---

## 6. Access Jira Setup

In browser:

```
http://<server-ip>:8080
```

Complete setup wizard:

* Connect PostgreSQL database
* Create admin user
* Import or start with empty site

---

## 7. Generate Jira Evaluation License

1. Visit: [https://my.atlassian.com/license/evaluation](https://my.atlassian.com/license/evaluation)
2. Log in / create account
3. Select: **Jira Software (Data Center)**
4. Click **Generate Evaluation License**
5. Paste license key into Jira setup

---

## 8. Configure Base URL

```
Administration → System → General Configuration → Edit Settings
```

Set:

```
http://<server-ip>:8080
```

---

## 9. Configure Email SMTP

Install sendmail:

```
sudo apt install -y sendmail
```

Jira settings:

```
System → Outgoing Mail → SMTP
```

Values:

* From address: [jira@example.com](mailto:jira@example.com)
* SMTP Host: localhost
* Port: 25
* Email Prefix: [JIRA]

Test & save.

---

## 10. Create Project (DEV)

```
Projects → Create Project → Software → Scrum/Kanban
```

* Name: Development Project
* Key: DEV

---

## 11. Create Custom Workflow

```
Administration → Issues → Workflows → Add Workflow
```

Statuses:

* To Do
* In Progress
* In Review
* QA Testing
* Done

Link transitions accordingly.

---

## 12. Create Screens & Screen Scheme

### Screen

```
Administration → Issues → Screens → Add Screen
```

Add fields:

* Assignee
* Priority
* Comment
* Fix Version
* Component

### Screen Scheme

```
Administration → Issues → Screen Schemes
```

Use same screen for Create/Edit/View.

---

## 13. Issue Type Scheme

```
Administration → Issues → Issue Type Schemes
```

Add:

* Story
* Bug
* Task
* Subtask
  Assign scheme to DEV project.

---

## 14. Permission Scheme

```
Administration → Issues → Permission Schemes
```

Permissions setup:

* Administer Projects → admin
* Browse Projects → admin, developer, tester
* Create Issues → admin, developer
* Edit Issues → admin, developer
* Add Comments → all roles
* Resolve Issues → admin, developer

Assign to DEV project.

---

## 15. Notification Scheme

```
Administration → Issues → Notification Schemes
```

Common notifications:

* Issue Created → Project Lead
* Issue Updated → Current Assignee
* Issue Resolved → Reporter

Assign to DEV project.

---

## 16. Reindex Jira

```
Administration → System → Indexing → Full Reindex
```

Wait for completion.

---

## 17. Enable Jira on Boot

```
sudo systemctl enable jira
sudo systemctl start jira
```

---

## 18. Create Sample Issues

Examples:

* DEV-1 – Setup environment
* DEV-2 – Implement login
* DEV-3 – API integration
* DEV-4 – UI updates

---

## 19. Verify System Health

```
Administration → System → System Info
```

Check:

* Java Version = 17 or 21
* Database = PostgreSQL
* Uptime
* Index status

---

## Day 10 Completed

You now have a fully working Jira 10.x installation with:

* Java 21
* PostgreSQL backend
* Custom workflows
* Screens, schemes, and permissions
* Email notifications
* Base URL configuration
* Service auto-start
