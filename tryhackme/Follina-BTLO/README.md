# 🦠 Follina — Malware Analysis Challenge

**Platform:** Blue Team Labs Online (BTLO)  
**Category:** Malware Analysis / OSINT / Threat Intelligence  
**Difficulty:** Easy (10 points)  
**Tools:** Kali Linux, VirusTotal, unzip, sha1sum, grep  
**Date:** July 17, 2026

---

## Scenario

> "On a Friday evening when you were in a mood to celebrate your weekend,
> your team was alerted with a new RCE vulnerability actively being exploited in the wild.
> You have been tasked with analyzing and researching the sample to collect information for the weekend team."

---

## ⚙️ Environment Setup

The challenge file comes as a `.zip` with password `infected` — standard for malware samples
to prevent accidental execution and antivirus from deleting the file before analysis.

**Safety rules followed:**
- Used Kali Linux VM with **Host-Only** network mode (isolated from the internet)
- Never executed the malware — performed **static analysis only**
- Analyzed results on the host machine through the VM screen

**Transferring the file to the VM:**
```bash
# On the host machine (inside the Downloads folder)
python3 -m http.server 8000

# Inside Kali Linux VM
wget http://[HOST_IP]:8000/Challenge.zip
unzip Challenge.zip  # password: infected
```

---

## 🔬 Static Analysis — Step by Step

### Step 1 — Identify the file
```bash
ls -la
# Output: sample.doc (but structure reveals it's actually an OpenXML format)

file sample.doc
# Output: Microsoft Word 2007+ document
```

Although the extension is `.doc`, the binary content showed XML structures typical of `.docx` (OpenXML):
```
word/document.xml
[Content_Types].xml
word/_rels/document.xml.rels
word/theme/theme1.xml
```
A `.docx` file is actually a **ZIP archive** containing XML files. Attackers disguise `.docx` as `.doc`
to confuse antivirus engines and trigger compatibility mode vulnerabilities.

---

### Step 2 — Generate the hash (file fingerprint)
```bash
sha1sum sample.doc
# Output: 06727ffda60359236a8029e0b3e8a0fd11c23313
```

The hash is the unique "fingerprint" of a file. If even one byte changes, the hash changes completely.
Used to identify the exact malware sample across threat intelligence databases.

### 🖼️ Screenshot — SHA1 hash and file extraction
![SHA1 and unzip](prints/sha1-unzip.png)

---

### Step 3 — Extract and inspect the document structure
```bash
unzip sample.doc -d conteudo_extract
cd conteudo_extract/word/_rels/
grep -i "External" document.xml.rels
```

The `document.xml.rels` file stores all **external relationships** — links the document
tries to access when opened. This is where the malicious URL was hidden.

### 🖼️ Screenshot — Grep finding the malicious external URL
![Grep external URL](prints/grep-external-url.png)

The `grep` output revealed a `Target` attribute pointing to an external URL with `TargetMode="External"` —
this is the link the document silently contacts when opened in Microsoft Word.

---

## 📋 VirusTotal Analysis

Searched for the SHA1 hash on VirusTotal to gather threat intelligence.

**Key findings:**
- **File name:** `sample.docx`
- **File type:** Office Open XML Document
- **Community Score:** -540 (highly malicious)
- **Tags:** `docx`, `exploit`, `cve-2022-30190`, `calls-wmi`, `cve-2017-0199`
- **Detections:** 65/[total] antivirus engines flagged it

### 🖼️ Screenshot — VirusTotal Relations tab showing contacted URLs
![VirusTotal Relations](prints/virustotal-relations.png)

### 🖼️ Screenshot — MITRE ATT&CK technique T1559
![MITRE T1559](prints/mitre-t1559.png)

---

## 🔍 Challenge Questions & Answers

### Q1 — SHA1 hash of the sample
```bash
sha1sum sample.doc
```
**Answer:** `06727ffda60359236a8029e0b3e8a0fd11c23313`

---

### Q2 — Full filetype according to VirusTotal
Found in the **Details** tab on VirusTotal under "File type".

**Answer:** `Office Open XML Document`

---

### Q3 — Malicious URL embedded in the sample
```bash
unzip sample.doc -d conteudo_extract
cd conteudo_extract/word/_rels/
grep -i "External" document.xml.rels
```
Look for the `Target=` attribute with `TargetMode="External"`.

**Answer:** `https://www.xmlformats.com/office/word/2022/wordprocessingDrawing/RDF842l.html`

---

### Q4 — XML file storing the extracted URL
The URL was found inside:

**Answer:** `document.xml.rels`

---

### Q5 — Minimum file size to invoke the payload
The Follina exploit abuses the `mshtml.dll` Windows component which has a hardcoded buffer size.
Files smaller than this threshold are processed differently and don't trigger the exploit.
Attackers pad their HTML files with junk data (repeated characters) to exceed this limit.

**Answer:** `4096`

---

### Q6 — Process the sample tries to kill on execution
**How to find it:**
The Follina exploit invokes `msdt.exe` (Microsoft Support Diagnostic Tool) to execute commands.
To hide its tracks, the malware immediately tries to kill this process so no diagnostic window appears.

**Detection method used:** OSINT — searching for `CVE-2022-30190 taskkill` reveals the process name.
**Also visible in:** VirusTotal BEHAVIOR tab → Command Executed section.

**Answer:** `msdt.exe`

---

### Q7 — ProcessName and ParentProcessName for Windows Event ID 4688 detection rule

**Windows Event ID 4688** = "A new process was created"

Every time a process spawns another, Event 4688 records:
- `ParentProcessName` = the program that gave the order
- `ProcessName` = the program that was created

**Attack chain:**
```
User opens sample.doc
        ↓
winword.exe reads the document
        ↓
Follina exploit triggers
        ↓
winword.exe spawns msdt.exe
        ↓
msdt.exe executes malicious PowerShell commands
```

**OSINT approach:** Search for `CVE-2022-30190 sigma rule 4688` on GitHub (SigmaHQ) to find:
```yaml
ParentImage|endswith: '\winword.exe'
Image|endswith: '\msdt.exe'
```

**Answer:** `msdt.exe, winword.exe`

---

### Q8 — MITRE ATT&CK Execution technique ID

Found in VirusTotal → **BEHAVIOR** tab → MITRE ATT&CK Tactics and Techniques → Execution section.

**Technique:** Inter-Process Communication (IPC)
**Sub-technique:** Dynamic Data Exchange (DDE)

DDE is a legitimate Microsoft feature that allows Office programs to share data dynamically.
Attackers abuse it to make Word silently fetch and execute content from an external URL.

**Answer:** `T1559`

---

### Q9 — CVE associated with the vulnerability

The popular name "Follina" maps to the official CVE identifier.
**OSINT method:** Search `Follina CVE` on Google.

**Answer:** `CVE-2022-30190`

---

## 🧠 Key Concepts Learned

| Concept | Definition |
|---------|-----------|
| **Static Analysis** | Examining a file without executing it |
| **OpenXML / DOCX** | `.docx` files are ZIP archives containing XML files |
| **SHA1 Hash** | 40-character unique fingerprint of a file |
| **External Relationship** | Links inside Office documents that fetch remote content |
| **Follina (CVE-2022-30190)** | Vulnerability that abuses ms-msdt:// URL handler to execute commands |
| **msdt.exe** | Microsoft Support Diagnostic Tool — abused by Follina |
| **winword.exe** | Microsoft Word process — the malicious parent process in this attack |
| **Event ID 4688** | Windows log: "A new process was created" |
| **MITRE T1559** | Inter-Process Communication — technique used for execution |
| **DDE** | Dynamic Data Exchange — legitimate feature abused for code execution |
| **Sigma Rule** | Community-standard format for writing detection rules |

---

## 🔗 Attack Flow Summary

```
[sample.doc opened]
        ↓
winword.exe loads document
        ↓
External relationship in document.xml.rels triggers
        ↓
Word fetches malicious HTML from xmlformats.com
        ↓
HTML exploits mshtml.dll (file must be > 4096 bytes)
        ↓
msdt.exe is invoked via ms-msdt:// URL handler
        ↓
Malicious PowerShell commands execute
        ↓
taskkill /f /im msdt.exe (hide evidence)
        ↓
Payload delivered
```

This was my **first real malware analysis** using the BTLO platform,
working with an actual malicious sample in an isolated Kali Linux environment.
