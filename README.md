<div align="center">

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=28&pause=1000&color=00D9FF&center=true&vCenter=true&width=700&lines=Web+Attack+Detection+Lab;Traffic+Analysis+%26+Packet+Inspection;Ethical+Cybersecurity+Research" alt="Typing SVG" />

<br/>

![Kali Linux](https://img.shields.io/badge/Kali_Linux-557C94?style=for-the-badge&logo=kalilinux&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Wireshark](https://img.shields.io/badge/Wireshark-1679A7?style=for-the-badge&logo=wireshark&logoColor=white)
![Nmap](https://img.shields.io/badge/Nmap-214478?style=for-the-badge&logo=nmap&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)

<br/>

> **A controlled cybersecurity lab for simulating, capturing, and analyzing common web attack traffic using DVWA, Wireshark, Nmap, and Gobuster.**

</div>

---

## 📌 Overview

This project is a hands-on **Web Attack Detection & Traffic Analysis Lab** built on **Kali Linux**. It simulates real-world web attack scenarios — SQL Injection, XSS, Port Scanning, and Directory Enumeration — in a safe, isolated Docker environment.

Captured network packets are analyzed using **Wireshark** to study how each attack manifests in traffic and how it can be detected through patterns and filters.

---

## 🎯 Objectives

- Deploy a deliberately vulnerable web app (DVWA) using Docker
- Generate both normal and malicious HTTP/TCP traffic
- Capture and inspect packets using Wireshark
- Identify and document attack signatures and detection techniques
- Build a reproducible lab for security learning and demonstration

---

## 🛠️ Tools & Technologies

| Tool | Role |
|------|------|
| 🐉 **Kali Linux** | Penetration testing OS |
| 🐳 **Docker** | Container runtime for DVWA |
| 🌐 **DVWA** | Deliberately Vulnerable Web App |
| 📡 **Wireshark** | Network packet capture & analysis |
| 🔍 **Nmap** | Port scanning & service detection |
| 🚀 **Gobuster** | Directory & file enumeration |
| 📝 **Git & GitHub** | Version control & documentation |

---

## ⚙️ Setup & Installation

### Prerequisites

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install docker.io wireshark nmap gobuster git -y
```

### Start Docker

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### Deploy DVWA

```bash
# Pull the DVWA image
sudo docker pull vulnerables/web-dvwa

# Run DVWA on port 8080
sudo docker run -d -p 8080:80 vulnerables/web-dvwa
```

### Access the Application

Open your browser and navigate to:

```
http://127.0.0.1:8080
```

| Field | Value |
|-------|-------|
| Username | `admin` |
| Password | `password` |
| Security Level | `Low` |

> After login, click **"Create / Reset Database"** on the setup page, then log in again.

---

## 🔥 Attacks Simulated

### 1. 🔍 Port Scanning — Nmap

```bash
nmap -sV 127.0.0.1
```

**What it does:** Discovers open ports and identifies running services on the target host.  
**Traffic signature:** Burst of TCP SYN packets across multiple ports.

---

### 2. 💉 SQL Injection

Navigate to: `DVWA → SQL Injection`

```sql
' OR 1=1#
```

**What it does:** Bypasses authentication or leaks database contents by injecting SQL logic.  
**Traffic signature:** HTTP GET/POST requests with encoded SQL characters (`%27`, `OR`, `=`, `#`).

---

### 3. 🧨 Cross-Site Scripting (XSS)

Navigate to: `DVWA → XSS (Reflected)`

```html
<script>alert('XSS')</script>
```

**What it does:** Injects executable JavaScript into a web page response.  
**Traffic signature:** HTTP requests containing `<script>` tags or encoded JS payloads.

---

### 4. 📂 Directory Enumeration — Gobuster

```bash
gobuster dir -u http://127.0.0.1:8080 -w /usr/share/wordlists/dirb/common.txt
```

**What it does:** Brute-forces hidden directories and files on the web server.  
**Traffic signature:** Hundreds of rapid HTTP GET requests with 404/403/200 responses.

---

## 📊 Wireshark Traffic Analysis

### Starting a Capture

```bash
# Capture on loopback interface
sudo wireshark &
# Select interface: lo (loopback)
```

### Key Filters Used

| Purpose | Wireshark Filter |
|---------|-----------------|
| All HTTP traffic | `http` |
| TCP traffic | `tcp` |
| SYN scan detection | `tcp.flags.syn==1 && tcp.flags.ack==0` |
| SQL injection patterns | `http contains "OR" && http contains "="` |
| XSS payloads | `http contains "script"` |
| Specific target IP | `ip.addr == 127.0.0.1` |
| High request volume | `http.request.method == "GET"` |

### Traffic Observations

| Attack | Observable Pattern |
|--------|-------------------|
| Nmap Scan | Rapid SYN packets to sequential ports, no full handshake |
| SQL Injection | URL-encoded characters: `%27`, `%20OR%20`, `%23` |
| XSS | `<script>` tags visible in HTTP payload |
| Gobuster | Hundreds of requests/sec, high 404 rate, uniform User-Agent |

---

## 📁 Repository Structure

```
web-attack-detection/
│
├── 📁 pcaps/
│   └── README.md                   # Instructions for pcap files
│
├── 📁 reports/
│   └── findings.md                 # Detailed findings & analysis
│
├── .gitignore
├── CONTRIBUTING.md
└── README.md
```

---

## 🔍 Key Findings

- **Nmap SYN scans** produced a distinct burst of TCP SYN packets with no corresponding ACK — a classic half-open scan fingerprint.
- **Gobuster enumeration** generated an abnormally high volume of sequential GET requests with a near-identical User-Agent string — easily detectable as automated.
- **SQL Injection payloads** appeared as URL-encoded parameters in HTTP traffic (e.g., `id=%27+OR+1%3D1%23`).
- **XSS payloads** were visible as raw `<script>` content within HTTP response bodies when security level was set to Low.
- Attack traffic was **clearly distinguishable** from normal browsing through packet rate, payload content, and response code distribution.

---

## 🚀 Skills Demonstrated

- Network Packet Capture & Inspection
- Web Application Penetration Testing
- Attack Pattern Recognition & Documentation
- Linux System Administration
- Docker Container Management
- Cybersecurity Reporting

---

## ⚠️ Disclaimer

> This project was conducted entirely in a **controlled, isolated lab environment** for **educational purposes only**.  
> All attacks were performed against a local, intentionally vulnerable application (DVWA).  
> **Never perform security testing on systems you do not own or have explicit written permission to test.**  
> The author holds no responsibility for any misuse of the techniques demonstrated here.

---

<div align="center">

⭐ **Star this repo if it helped you learn something!**

[![GitHub stars](https://img.shields.io/github/stars/Debasish-Nayak-656/web-attack-detection?style=social)](https://github.com/Debasish-Nayak-656/web-attack-detection)

</div>
