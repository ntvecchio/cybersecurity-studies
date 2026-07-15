# 🌐 Web Application Security Fundamentals

**Platform:** TryHackMe  
**Level:** ⭐ Easy  
**Category:** Blue Team / Web Security  
**Date:** July 13, 2026

---

## Why Web Application Security Matters

Over the past decades, applications shifted from desktop to web-based.
Today nearly everything runs in a browser — email, banking, social media, even lab machines.

This shift brings advantages but also **serious security tradeoffs**:

| As a Web App Owner | As a Web App User |
|--------------------|------------------|
| App is exposed 24/7 | Data stored potentially insecurely |
| Anyone worldwide can try to attack | Browser breach = all accounts at risk |
| Responsible for securing users' data | Risk of identity theft or financial loss |

### Real-world examples
- **Equifax (2017):** 150 million Americans' data exposed via an Apache Struts vulnerability
- **Capital One (2019):** 100 million customers affected by a misconfigured WAF

> **Key principle:** Whoever collects and stores data is responsible for protecting it.
> The **Web App Owner** is always ultimately responsible for user data security.

---

## 🔄 The Request-Response Cycle

Every interaction with a website follows this cycle:

```
[Browser] --- Request ---> [Web Server]
                                |
                          Processes request
                                |
[Browser] <-- Response --- [Web Server]
```

- **GET request** → retrieve a resource (web page, image)
- **POST request** → submit data (login credentials, forms)

Attackers abuse this cycle by:
- Overwhelming servers with requests (DDoS)
- Bypassing access controls
- Tricking the server into executing harmful commands

---

## 🏗️ Three Components of Any Web Service

| Component | What it is | Example |
|-----------|-----------|---------|
| **Application** | The code, images, styles — what you see and interact with | WordPress, Python Flask app |
| **Web Server** | Software that listens for requests and returns responses | Apache, Nginx, IIS |
| **Host Machine** | The underlying OS running everything | Ubuntu Linux, Windows Server |

### Most common web servers

| Server | Used by |
|--------|---------|
| **Apache** | Most popular — WordPress sites |
| **Nginx** | High-performance apps — Netflix, Airbnb, GitHub |
| **IIS** | Microsoft enterprise environments |

---

## 🛡️ Defense in Depth — Protecting All Three Layers

The idea is simple: **never rely on just one security barrier.**
If an attacker bypasses the application, they hit the web server.
If they bypass the web server, the host machine stops them.

### Protecting the Application
- **Secure Coding:** avoid insecure functions, handle errors properly
- **Input Validation & Sanitization:** never trust user input — validate everything server-side
- **Access Control:** restrict access based on user roles

> 🔑 Golden rule in web development: **"Never trust data coming from the user."**
> Everything submitted via browser must be validated on the back-end before processing.

### Protecting the Web Server
- **Access Logs:** detailed record of every request (IP, timestamp, page, status code, user agent)
- **WAF (Web Application Firewall):** filters and blocks harmful traffic
- **CDN (Content Delivery Network):** reduces direct server exposure

### Protecting the Host Machine
- **Least Privilege:** never run services as root — use low-privilege users (e.g. `www-data`)
- **System Hardening:** disable unnecessary services, close unused ports
- **Antivirus:** blocks known malware at endpoint level

### Applies to all three layers
- **Strong Authentication:** protect admin panels and host access
- **Patch Management:** keep app dependencies, web server, and OS up to date

> Most successful breaches happen because organizations forget to update their systems.
> A simple `sudo apt update && sudo apt upgrade` solves a huge portion of known vulnerabilities.

---

## 📋 Access Logs

Web servers log every request received. These logs are invaluable for security investigations.

**Fields typically recorded:**
- Client IP address
- Timestamp
- Requested page or resource
- HTTP method (GET, POST)
- Response status code
- User agent (browser/tool used)

**Example log sequence (benign):**
```
10.10.10.100 GET /index.html       → user visits homepage
10.10.10.100 GET /login.html       → navigates to login
10.10.10.100 POST /login.html      → submits credentials
10.10.10.100 GET /myaccount.html   → accesses account page
```

Even normal traffic like this helps analysts reconstruct attack sequences during incident response.

---

## 🌍 CDN (Content Delivery Network)

CDNs store cached content on servers closer to the user to reduce latency.

```
[User in Brazil] → [Edge server in São Paulo] → [Origin server in USA]
```

### Security benefits

| Benefit | How it helps |
|---------|-------------|
| **IP Masking** | Hides origin server IP — harder for attackers to target directly |
| **DDoS Protection** | Absorbs massive traffic spikes |
| **Enforced HTTPS** | Encrypted communication by default |
| **Integrated WAF** | Cloudflare, AWS CloudFront, Azure Front Door all include WAFs |

---

## 🔥 WAF (Web Application Firewall)

A WAF inspects incoming HTTP/HTTPS traffic and blocks harmful requests.

> Think of it as a **bouncer at a club** — every request must be checked before entering.

### Types of WAF

| Type | How it's deployed | Best for |
|------|------------------|----------|
| **Cloud-based** | Sits in front of the server (reverse proxy) | Easy to deploy, scalable |
| **Host-based** | Installed directly on the web server | Per-application control |
| **Network-based** | Physical/virtual appliance on network perimeter | Enterprise environments |

### Detection methods

| Method | How it works | Example |
|--------|-------------|---------|
| **Signature-Based** | Matches known attack patterns | Blocks requests from `sqlmap/1.8.1` |
| **Heuristic-Based** | Analyzes context and behavior | Flags long query strings with special chars |
| **Anomaly & Behavioral** | Flags deviations from normal traffic | Single IP making 1000 login attempts |
| **IP Reputation Filtering** | Blocks IPs from threat intel lists | Requests from known malicious IPs |

---

## 🦠 Antivirus (AV)

AVs primarily protect **endpoints** (desktops, laptops, servers) from known malicious files.

- Mostly relies on **signature-based detection**
- Plays a key role even in web security scenarios
- Can detect **malicious file uploads** such as web shells and post-exploitation tools

**Example scenario:**
An attacker finds a file upload vulnerability and tries to upload a web shell.
Even if the WAF misses it, the AV on the host machine scans the file and deletes it before execution.

> AV is just **one layer** — it must be combined with WAF, CDN, secure coding, and logging.

---

## 🧠 Key Concepts Summary

| Concept | Definition |
|---------|-----------|
| **Mitigation** | Stopping or limiting damage from threats |
| **Patch Management** | Keeping all software and components up to date |
| **Defense in Depth** | Multiple security layers so no single failure causes full compromise |
| **Least Privilege** | Services run with minimum permissions needed |
| **Input Validation** | Verifying user input before processing it server-side |
| **WAF** | Firewall specialized in filtering web traffic |
| **CDN** | Network of edge servers that cache content and provide security |
| **Access Log** | Record of every request a web server receives |

---

## ✅ Room Answers

| Question | Answer |
|----------|--------|
| Have apps shifted from desktop to web? | Yea |
| Who is responsible for user data security? | The Web App Owner |
| What does a browser send to receive a web page? | A request |
| Most common web server for WordPress? | Apache |
| Concept of stopping/limiting damage from threats? | Mitigation |
| Security control that keeps software up to date? | Patch Management |
| WAF type that runs on the same system as the app? | Host-based |
| WAF detection that matches known malicious patterns? | Signature-Based Detection |
