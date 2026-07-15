# 🕵️ Detecting Web Attacks

**Platform:** TryHackMe  
**Level:** ⭐ Easy  
**Category:** Blue Team / Web Security / SOC  
**Date:** July 13, 2026

---

## Overview

Web attacks are among the most common entry points for attackers.
Public-facing web applications often sit in front of databases and critical infrastructure,
making them high-value targets. This room covers how to **detect** web attacks using
logs, network traffic analysis, and WAFs.

---

## 🖥️ Client-Side Attacks

Attacks that exploit weaknesses in the **user's browser or behavior**.

> Limitation: Server-side logs and network captures offer **little to no visibility**
> into what happens inside a user's browser. These attacks are often hard to detect from a SOC perspective.

### Common Client-Side Attacks

| Attack | How it works |
|--------|-------------|
| **XSS (Cross-Site Scripting)** | Malicious scripts injected into a trusted site and executed in the victim's browser |
| **CSRF (Cross-Site Request Forgery)** | Browser tricked into sending unauthorized requests on behalf of the user |
| **Clickjacking** | Invisible elements overlaid on legitimate content to trick users into clicking |

**XSS Example:**
```html
Hello <script>alert('You have been hacked');</script>
```
If a comment box doesn't filter input, this script runs in every visitor's browser.
In a real attack, it could steal session cookies instead of showing a harmless popup.

---

## 🖧 Server-Side Attacks

Attacks that exploit vulnerabilities in the **web server, application code, or backend**.

> Advantage for defenders: server-side attacks **leave evidence** in logs and network traffic.

### Common Server-Side Attacks

| Attack | How it works | Real example |
|--------|-------------|-------------|
| **Brute Force** | Automated repeated login attempts | T-Mobile 2021 — 50M customers' PII exposed |
| **SQL Injection (SQLi)** | Malicious SQL commands injected via forms | MOVEit 2023 — 2,700+ organizations affected |
| **Command Injection** | System commands injected through user input | Server executes commands with app-level permissions |

---

## 📋 Log-Based Detection

### Access Log Format

| Field | Example Indicator |
|-------|-----------------|
| Client IP | Known malicious IP or unexpected geolocation |
| Timestamp | Requests at unusual hours or high frequency |
| Status Code | Many 404s = directory fuzzing |
| Response Size | Unusually small/large responses |
| Referrer | Pages that don't fit normal navigation |
| User-Agent | Attack tools like `sqlmap`, `wpscan`, `gobuster` |

### Attack Sequence in Logs

**Step 1 — Directory fuzzing**
```
GET /admin     200  ← valid page found
GET /login.php 200  ← valid page found
GET /backup    404
GET /config    404
```
Many 404s with occasional 200s = attacker mapping the application.

**Step 2 — Brute force attack**
```
POST /login.php 401  ← failed attempt
POST /login.php 401  ← failed attempt
POST /login.php 401  ← failed attempt
POST /login.php 302  ← SUCCESS (redirect to /account)
```
Repeated POST requests in quick succession = brute force.
Status `302 Found` = successful login redirect.

**Step 3 — SQL Injection attempts**
```
GET /search?q=' OR '1'='1     200
GET /search?q=1' OR 'a'='a   200
```
Special characters and SQL keywords in query strings = SQLi attempt.

### Log Limitations
Access logs **do not record POST body data** — you can see that a login was attempted
but not the actual credentials submitted. GET request query strings may or may not
be logged depending on the server configuration.

---

## 🔬 Network Traffic Analysis (Wireshark)

Network captures are **more verbose than logs** — they reveal the full content
of every request and response, including headers, POST bodies, cookies, and files.

> Limitation: Encrypted traffic (HTTPS) limits visibility without decryption keys.
> These examples focus on HTTP traffic.

### What network captures reveal that logs don't

| What logs show | What Wireshark adds |
|---------------|-------------------|
| POST /login — 302 | Actual username and password submitted |
| GET /search?q=... | Full SQLi payload and database response |
| GET /upload — 200 | Actual file content uploaded |

### Useful Wireshark filters
```
http                          # show only HTTP traffic
http.user_agent               # filter by user agent
ip.dst == 10.10.20.200        # filter by destination IP
```

**Right-click → Follow HTTP Stream** reconstructs the full request-response conversation.

### Attack sequence in Wireshark

**Brute force:** Repeated POST packets → last successful one reveals the valid password

**SQLi:** Inspect the HTTP packet body to see the exact payload used
and the data returned by the database (e.g. dumped Users table with names in plain text)

---

## 🔥 WAF — Web Application Firewall

WAFs act as **gatekeepers** — they inspect full request packets and filter them
before reaching the server. Unlike Wireshark, WAFs can also decrypt HTTPS traffic.

### WAF Rule Types

| Rule Type | Description | Example |
|-----------|-------------|---------|
| Block attack patterns | Blocks known malicious payloads | Block User-Agent: `sqlmap` |
| Deny malicious sources | IP reputation and geo-blocking | Block IPs from botnet campaigns |
| Custom rules | Tailored to your application | Allow only GET/POST to `/login` |
| Rate limiting | Prevents abuse by limiting request frequency | Max 5 login attempts/min per IP |

### Example — Custom WAF rule
```
If User-Agent contains "sqlmap"
then BLOCK
```

### Challenge-Response Mechanisms
WAFs can also **challenge** suspicious requests with CAPTCHA instead of blocking outright.
Useful when there's a higher chance of false positives (blocking legitimate users).

> 37% of global web traffic comes from malicious bots.

### Threat Intelligence Integration
Modern WAFs include:
- Built-in rule sets covering the **OWASP Top 10**
- Automatic blocking of known malicious IPs
- Regular updates for new CVEs and threat actor TTPs
- Curated IP lists: botnets, VPNs, anonymizers, malware (e.g. Cloudflare)

---

## 🧠 Key Concepts Summary

| Concept | Definition |
|---------|-----------|
| **Client-side attack** | Exploits user behavior or browser vulnerabilities |
| **Server-side attack** | Exploits web server, app code, or backend |
| **XSS** | Malicious scripts injected into trusted sites |
| **CSRF** | Browser tricked into unauthorized requests |
| **SQLi** | SQL commands injected to manipulate databases |
| **Brute Force** | Automated repeated login attempts |
| **Directory Fuzzing** | Automated scanning for valid pages/directories |
| **Access Log** | Record of every web server request |
| **WAF** | Firewall specialized in filtering web traffic |
| **Rate Limiting** | Restricts request frequency to prevent abuse |

---

## ✅ Room Answers

| Question | Answer |
|----------|--------|
| What class of attacks relies on exploiting user behavior or device? | Client-side attacks |
| What is the most common client-side attack? | Cross-Site Scripting (XSS) |
| What class of attacks exploits web server vulnerabilities? | Server-side attacks |
| Which server-side attack lets attackers dump database contents? | SQL Injection (SQLi) |
| What do WAFs inspect and filter? | Web requests |
