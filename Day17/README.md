# 📘 **Day 17 – Burp Suite Community Edition Practical Lab (README.md)**

**APT Admin Training – Day 17**
**Topic:** Web Application Security Basics using Burp Suite
**Platform:** Ubuntu (with GUI via RDP)

---

# 📝 **Overview**

This lab introduces the basics of web application security testing using **Burp Suite Community Edition**.
You will learn how to configure Burp as a proxy, intercept and inspect traffic, perform passive security scans, and generate a report of findings.

⚠️ **IMPORTANT:**
This lab uses a **safe, intentionally vulnerable test application (OWASP Juice Shop)**.
Do NOT scan real production systems.

---

# 🎯 **Objectives**

By the end of Day 17, you will be able to:

* Install and launch Burp Suite Community Edition
* Install and run a safe vulnerable web application (Juice Shop)
* Configure Firefox to route traffic through Burp
* Intercept & inspect HTTP requests
* Run passive vulnerability scanning
* Analyze the findings (headers, cookies, CORS, etc.)
* Save Burp project data
* Create a simple security report

---

# 🖥️ **Prerequisites**

* Ubuntu VM with GUI access (via XRDP/RDP)
* User with sudo privileges
* Internet access for downloads

---

# 🚀 **Step 1 — Launch Burp Suite**

Open terminal on Ubuntu Desktop:

```bash
~/BurpSuiteCommunity/BurpSuiteCommunity &
```

Burp starts — choose:

* **Temporary Project**
* **Use Burp Defaults**
* Click **Start Burp**

Burp Dashboard will appear.

---

# 🧪 **Step 2 — Install the Test Application (Juice Shop)**

This is a safe vulnerable web app used for training security tools.

Install Node.js & npm:

```bash
sudo apt update
sudo apt install -y nodejs npm
```

Install Juice Shop globally:

```bash
sudo npm install -g juice-shop
```

Run it:

```bash
juice-shop
```

Juice Shop runs on:

```
http://localhost:3000
```

Leave this terminal open.

---

# 🌐 **Step 3 — Configure Browser Proxy (Firefox)**

Burp acts as a proxy on:

```
127.0.0.1:8080
```

### Configure Firefox proxy:

1. Open Firefox
2. Go to: **Settings → General → Network Settings → Settings**
3. Select: **Manual proxy configuration**
4. Enter:

```
HTTP Proxy: 127.0.0.1
Port: 8080
```

☑ Enable **"Use this proxy for all protocols"**
Click **OK**

---

# 🟡 **Step 4 — Verify Burp is Capturing Traffic**

Open Firefox and visit:

```
http://localhost:3000
```

Go to Burp → **Proxy → HTTP History**

You should see many requests from Juice Shop.
This confirms Burp Proxy is working properly.

---

# 🔍 **Step 5 — Intercept Traffic (Manual Testing)**

In Burp →
**Proxy → Intercept**

Click: **Intercept is ON**

Now refresh the Juice Shop page.

You will see:

* Raw HTTP request
* Headers
* Cookies
* Host, User-Agent, etc.

You can:

* Forward the request
* Modify it
* Drop it

After testing, click:
**Intercept is OFF**

---

# 🟢 **Step 6 — Trigger Passive Scanning**

Burp Community does NOT support Active Scan.
But **passive scanning** automatically analyzes all traffic you browse.

Trigger passive scan by browsing:

* Login page
* Product page
* Search bar
* Feedback form
* Basket checkout

Burp → **Dashboard → Issue Activity**

You will start seeing issues:

* Missing security headers (X-Frame-Options, CSP)
* Cookie flags missing (Secure, HttpOnly)
* CORS configuration warnings
* Interesting behavior (reflected data)
* PII exposure indicators

---

# 📁 **Step 7 — Analyze Findings**

Go to:

**Target → Site Map → [http://localhost:3000](http://localhost:3000)**

Right-click:

**Engagement tools → Find issues**

Click each finding to see:

* Severity
* Confidence
* Description
* Remediation advice
* Request/Response pair

---

# 💾 **Step 8 — Save Burp Project File**

Burp → **Project → Save Project As**

Save as:

```
day17-burp-project.burp
```

This file contains:

* Captured requests
* Passively found issues
* Site tree
* Proxy history

---

# 📝 **Step 9 — Create a Lab Report**

Create a markdown file:

```bash
nano day17-report.md
```

Paste:

```markdown
# Day 17 – Burp Suite Practical Lab Report

## Overview
This report summarizes the activities performed during Day 17 of the APT Admin Training program. 
Burp Suite Community Edition was used to analyze HTTP traffic and perform passive security scanning 
on a safe test application (OWASP Juice Shop).

## Target Application
- URL: http://localhost:3000
- App: OWASP Juice Shop (intentionally vulnerable)

## Tools Used
- Burp Suite Community Edition
- Firefox Browser (Proxy enabled)
- Node.js + Juice Shop server

## Tasks Completed
1. Installed and launched Burp Suite Community Edition
2. Configured Firefox to use Burp as an HTTP proxy
3. Intercepted and inspected HTTP requests
4. Generated passive scan results by browsing the application
5. Analyzed identified vulnerabilities
6. Saved the Burp project for future reference

## Sample Findings
- **Missing Security Headers**
  - X-Frame-Options
  - Content-Security-Policy
- **Insecure Cookie Flags**
  - Missing HttpOnly  
  - Missing Secure flag
- **CORS Misconfiguration**
  - Wildcard origins allowed
- **Information Leakage**
  - Sensitive data in response headers

## Conclusion
All Day 17 objectives were completed successfully.  
Burp Suite was configured and used to inspect traffic, perform passive scanning, and identify common web vulnerabilities.

```

Save:

**CTRL + O → ENTER → CTRL + X**

---

# 🎉 **Day 17 Completed Successfully!**

You have now mastered:

✔ Burp installation
✔ Proxy setup
✔ Traffic interception
✔ Passive scanning
✔ Vulnerability analysis
✔ Report creation

---
