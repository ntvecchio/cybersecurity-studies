# 🐗 Snort — IDS/IPS in Practice

**Platform:** TryHackMe  
**Level:** ⭐⭐ Medium  
**Category:** Blue Team / Network Security / IDS-IPS  
**Date:** July 12-13, 2026  
**Extra environment:** Personal lab on Ubuntu

---

## What is Snort?

Snort is an open-source **NIDS/NIPS** created by Martin Roesch and maintained by Cisco Talos.
It is one of the most widely used tools in real SOC environments for monitoring and protecting networks.

**Version used:** 2.9.20 GRE (Build 82)

---

## 🧠 Core Concepts — IDS vs IPS

### The security guard analogy
- **IDS** = guard who **watches and alerts** — doesn't stop anyone
- **IPS** = guard who **watches and blocks** — acts immediately

| Acronym | Type | Scope | Acts? |
|---------|------|-------|-------|
| NIDS | Network IDS | Entire network | ❌ alerts only |
| HIDS | Host IDS | Single device | ❌ alerts only |
| NIPS | Network IPS | Entire network | ✅ blocks |
| HIPS | Host IPS | Single device | ✅ blocks |
| NBA | Behaviour-based | Entire network | ✅ blocks |

### Detection techniques
| Technique | How it works |
|-----------|-------------|
| **Signature-Based** | Matches traffic against known attack patterns |
| **Behaviour-Based** | Learns normal traffic and flags anomalies (requires *baselining*) |
| **Policy-Based** | Compares traffic against organizational security policies |

---

## ⚙️ Snort Modes

| Mode | What it does |
|------|-------------|
| **Sniffer** | Reads and displays packets on screen in real time |
| **Logger** | Captures and saves packets to a file |
| **IDS/IPS** | Analyzes traffic using rules and generates alerts or blocks |

---

## 📌 First Commands

```bash
# Check version and build number
snort -V

# Test configuration file
sudo snort -c /etc/snort/snort.conf -T
```

### 🖼️ Screenshot — Config validated on personal Ubuntu
![Config validated](prints/snort-config-validada.png)

> Snort running on interface **wlo1** (Wi-Fi).
> The message **"Snort successfully validated the configuration!"** confirms everything is set up correctly.

---

## 👁️ Sniffer Mode

| Parameter | What it shows |
|-----------|--------------|
| `-v` | Source/destination IP, protocol |
| `-d` | + packet payload content |
| `-e` | + MAC address (link layer) |
| `-X` | Full packet in HEX |
| `-i interface` | Define which network interface to listen on |

```bash
sudo snort -v -i wlo1     # basic
sudo snort -vd            # + payload
sudo snort -X             # full HEX dump
```

### 🖼️ Screenshot — Sniffer mode -X output (full HEX)
![Snort -X mode](prints/snort-modo-x.png)

> Real-time IPv6 UDP traffic captured.
> In `-X` mode, every byte of the packet is displayed in hexadecimal with ASCII text alongside.

---

## 💾 Logger Mode

```bash
# Save in binary format
sudo snort -dev -l .

# Save in ASCII format (human-readable)
sudo snort -dev -K ASCII -l .

# Read a saved log
sudo snort -r snort.log.XXXXXXXXXX

# Filter while reading
sudo snort -r snort.log.XXXXXXXXXX 'tcp and port 80'
sudo snort -r snort.log.XXXXXXXXXX -n 10
```

| Format | Output | Who can read it |
|--------|--------|----------------|
| Binary (default) | Single `.log` file | Snort, tcpdump, Wireshark |
| ASCII (`-K ASCII`) | Folders named by IP | Any text editor |

> ⚠️ Logs saved with `-K ASCII` **cannot** be read with `-r`. Incompatible formats!

---

## 🚨 IDS/IPS Mode

### Parameters

| Parameter | Function |
|-----------|----------|
| `-c file.conf` | Define configuration file |
| `-A console` | Fast alerts on terminal |
| `-A cmg` | Alerts + payload in HEX on terminal |
| `-A full` | Full alerts (file only) |
| `-A fast` | Brief alerts (file only) |
| `-A none` | Disable alerts |
| `-N` | Disable logging |
| `-D` | Run in background (daemon) |
| `-q` | Quiet mode (no banner) |

```bash
# Real-time alerts on terminal
sudo snort -c /etc/snort/snort.conf -A console

# Save full alerts to file
sudo snort -c /etc/snort/snort.conf -A full -l .

# Run in background
sudo snort -c /etc/snort/snort.conf -D
ps -ef | grep snort        # check running process
sudo kill -9 PID           # stop process
```

### Output difference

```
IDS (alert):  [**] [1:10000001:0] ICMP Packet found [**]
IPS (block):  [Drop] [**] [1:10000001:0] ICMP Packet found [**]
```

---

## 🏋️ Practical Exercises — TryHackMe

### TASK-6 — Log analysis with Snort
```bash
sudo snort -dev -K ASCII -l .
```

#### 🖼️ Screenshot — TASK-6 result
![TASK-6](prints/task6-resultado.png)

**Results:**
- Snort processed **4 packets**
- TCP traffic: `145.254.160.237 → 65.208.228.223:80`

---

### TASK-7 — IDS mode with HTTP traffic
```bash
sudo snort -c /etc/snort/snort.conf -A full -l .
sudo ./traffic-generator.sh  # choose TASK-7
```

#### 🖼️ Screenshot — TASK-7 result
![TASK-7](prints/task7-http-get.png)

**Results found in output:**
- **HTTP GET methods detected:** `2` ✅
- HTTP Request Headers extracted: 127
- HTTP Response Headers extracted: 3
- Total packets processed: 5136

---

## 📁 PCAP Investigation

```bash
# Analyze a single PCAP file
sudo snort -c /etc/snort/snort.conf -q -r file.pcap -A console

# Analyze multiple PCAPs
sudo snort -c /etc/snort/snort.conf -q --pcap-list="a.pcap b.pcap" -A console

# Show which PCAP triggered each alert
sudo snort -c /etc/snort/snort.conf -q --pcap-list="a.pcap b.pcap" -A console --pcap-show
```

---

## 📝 Snort Rules

### Full structure
```
alert  tcp  any  any  ->  192.168.1.0/24  80  (msg:"Message"; sid:1000001; rev:1;)
action proto src  src  dir     dst        dst       options
```

### Actions
| Action | What it does |
|--------|-------------|
| `alert` | Generate alert and log |
| `log` | Log only |
| `drop` | Block and log (IPS) |
| `reject` | Block, log and notify sender |

### Direction
```
->   source → destination
<>   bidirectional (there is no <- in Snort!)
```

### IP and Port filtering
```bash
# Specific IP
alert icmp 192.168.1.56 any <> any any (msg:"Specific IP"; sid:1000001; rev:1;)

# Subnet
alert icmp 192.168.1.0/24 any <> any any (msg:"Subnet"; sid:1000001; rev:1;)

# Exclude IP (!)
alert icmp !192.168.1.0/24 any <> any any (msg:"External"; sid:1000001; rev:1;)

# Specific port
alert tcp any any -> any 22 (msg:"SSH"; sid:1000001; rev:1;)

# Port range
alert tcp any any -> any 1:1024 (msg:"System ports"; sid:1000001; rev:1;)

# Multiple ports
alert tcp any any -> any [21,23] (msg:"FTP or Telnet"; sid:1000001; rev:1;)
```

### Payload options
```bash
# Search for text inside packet
alert tcp any any -> any 80 (msg:"GET found"; content:"GET"; sid:1000001; rev:1;)

# Case insensitive
alert tcp any any -> any 80 (msg:"GET found"; content:"GET"; nocase; sid:1000001; rev:1;)
```

### Non-payload options (header)
```bash
# Filter by IP ID
alert any any -> any any (msg:"IP ID filter"; id:35369; sid:1000001; rev:1;)

# TCP flags
alert tcp any any -> any any (msg:"SYN scan"; flags:S; sid:1000001; rev:1;)
alert tcp any any -> any any (msg:"Push-ACK"; flags:PA; sid:1000002; rev:1;)

# Same source and destination IP
alert udp any any <> any any (msg:"Same IP src/dst"; sameip; sid:1000001; rev:1;)

# Payload size
alert ip any any <> any any (msg:"Large packet"; dsize:100<>300; sid:1000001; rev:1;)
```

### TCP Flags reference
| Flag | Letter | Meaning |
|------|--------|---------|
| SYN | `S` | Connection start — used in scans |
| ACK | `A` | Acknowledgement |
| FIN | `F` | Connection end |
| RST | `R` | Reset/abort |
| PSH | `P` | Push data immediately |
| URG | `U` | Urgent |

### SID numbering rule
```
< 100           → Reserved
100 - 999,999   → Built-in Snort rules
>= 1,000,000    → User-created rules
```

---

## 🧪 Personal Lab — Ubuntu

Beyond the TryHackMe room, I installed Snort on my own Ubuntu machine
and created custom rules to test in a real environment.

### Custom rule — detect pings with Portuguese alert message

```bash
# /etc/snort/rules/local.rules
alert icmp any any <> any any (msg:"ALERTA: alguem esta pingando nossa maquina!"; sid:1000001; rev:1;)
```

### Running on Wi-Fi interface (wlo1)
```bash
sudo snort -A console -q -c /etc/snort/snort.conf -i wlo1
```

#### 🖼️ Screenshot — Custom rule detecting real pings
![Custom rule](prints/snort-regra-personalizada.png)

> The rule fired on real **IPV6-ICMP** traffic from the local network.
> This confirms Snort is working correctly in the personal environment.

---

### BAD-TRAFFIC — same source and destination IP

Snort also automatically detected traffic with the **same source and destination IP**
(suspicious behavior associated with loops or attacks):

```
[1:527:8] BAD-TRAFFIC same SRC/DST
{UDP} 0.0.0.0:68 -> 255.255.255.255:67
```

#### 🖼️ Screenshot — BAD-TRAFFIC detected
![Bad traffic](prints/snort-bad-traffic.png)

---

## ⚙️ Configuration file (snort.conf)

```bash
sudo nano /etc/snort/snort.conf
sudo nano /etc/snort/rules/local.rules
```

### Key variables
| Variable | Purpose | Example |
|----------|---------|---------|
| `HOME_NET` | Your protected network | `192.168.1.0/24` |
| `EXTERNAL_NET` | External network | `!$HOME_NET` |
| `RULE_PATH` | Rules directory | `/etc/snort/rules` |

### Rule types available
| Type | Cost | Update frequency |
|------|------|-----------------|
| Community | Free, no registration | Less frequent |
| Registered | Free, registration required | 30-day delay |
| Subscriber | Paid | Twice a week |

---

## 🚀 Quick Cheatsheet

```bash
# Verification
snort -V
sudo snort -c /etc/snort/snort.conf -T

# Sniffer
sudo snort -v -i wlo1
sudo snort -X

# Logger
sudo snort -dev -l .
sudo snort -dev -K ASCII -l .
sudo snort -r file.log 'tcp and port 80'

# IDS/IPS
sudo snort -c /etc/snort/snort.conf -A console
sudo snort -c /etc/snort/snort.conf -A full -l .

# PCAP
sudo snort -c /etc/snort/snort.conf -q -r file.pcap -A console

# Rules
sudo nano /etc/snort/rules/local.rules
rm alert    # clear previous alerts
```
