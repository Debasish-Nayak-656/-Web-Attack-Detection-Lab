# 🔍 Security Findings Report

**Project:** Web Attack Detection & Traffic Analysis Lab  
**Environment:** Kali Linux + Docker (DVWA)  
**Date:** June 2025  
**Author:** Debasish Nayak  

---

## 1. Port Scanning (Nmap SYN Scan)

### Method
```bash
nmap -sV 127.0.0.1
```

### Observations
- Nmap sent TCP SYN packets to all common ports (1–1000+)
- No full three-way handshake was completed (half-open scan)
- Port 8080 was identified as open, running HTTP (Apache via Docker)

### Detection Indicators
| Indicator | Detail |
|-----------|--------|
| Packet pattern | SYN flood across sequential ports |
| Wireshark filter | `tcp.flags.syn==1 && tcp.flags.ack==0` |
| Time window | ~2–5 seconds for full scan |
| Anomaly | No full TCP handshake on most ports |

### Risk Level: 🟡 Medium
- Reveals open ports and service versions
- Precursor to targeted exploitation

---

## 2. SQL Injection

### Method
Navigated to DVWA → SQL Injection and entered:
```sql
' OR 1=1#
```

### Observations
- Application returned all user records from the database
- No input validation or parameterized queries in place
- HTTP GET request contained raw SQL characters in the `id` parameter

### Captured HTTP Request (Example)
```
GET /vulnerabilities/sqli/?id=%27+OR+1%3D1%23&Submit=Submit HTTP/1.1
Host: 127.0.0.1:8080
```

### Detection Indicators
| Indicator | Detail |
|-----------|--------|
| URL pattern | `%27` (single quote), `OR`, `%23` (#) in parameters |
| Wireshark filter | `http contains "OR" && http contains "="` |
| Response size | Abnormally large — returned all DB records |
| Anomaly | Single parameter triggering full DB dump |

### Risk Level: 🔴 Critical
- Can expose entire database contents
- May allow authentication bypass or data manipulation

---

## 3. Cross-Site Scripting (XSS)

### Method
Navigated to DVWA → XSS (Reflected) and entered:
```html
<script>alert('XSS')</script>
```

### Observations
- Browser executed the injected JavaScript
- Alert box appeared confirming successful XSS
- Script tag was visible in both request and response packets

### Captured HTTP Request (Example)
```
GET /vulnerabilities/xss_r/?name=<script>alert('XSS')</script> HTTP/1.1
Host: 127.0.0.1:8080
```

### Detection Indicators
| Indicator | Detail |
|-----------|--------|
| URL/body pattern | `<script>`, `alert(`, `onerror=` in parameters |
| Wireshark filter | `http contains "script"` |
| Anomaly | HTML tags present in GET/POST parameters |

### Risk Level: 🔴 High
- Can steal session cookies
- Enables phishing and credential theft

---

## 4. Directory Enumeration (Gobuster)

### Method
```bash
gobuster dir -u http://127.0.0.1:8080 -w /usr/share/wordlists/dirb/common.txt
```

### Observations
- Gobuster sent hundreds of HTTP GET requests in rapid succession
- Discovered paths: `/admin`, `/config`, `/login`, `/setup.php`, `/phpinfo.php`
- All requests used the same User-Agent string

### Detection Indicators
| Indicator | Detail |
|-----------|--------|
| Request rate | 50–200 requests/second |
| Response codes | Mostly 404, with some 200/403 hits |
| User-Agent | `gobuster/3.x` or generic string — non-browser |
| Wireshark filter | `http.request.method == "GET"` + high rate |

### Risk Level: 🟡 Medium
- Reveals application structure to attacker
- May expose backup files, config files, admin panels

---

## Summary Table

| Attack | Severity | Detectable? | Detection Method |
|--------|----------|-------------|-----------------|
| Port Scan (Nmap) | Medium | ✅ Yes | SYN flood, no handshake |
| SQL Injection | Critical | ✅ Yes | Encoded SQL in URL params |
| XSS | High | ✅ Yes | Script tags in HTTP data |
| Dir Enumeration | Medium | ✅ Yes | Request rate + User-Agent |

---

## Recommendations

1. **Input Validation** — Sanitize and validate all user inputs server-side.
2. **Parameterized Queries** — Use prepared statements to prevent SQL injection.
3. **WAF Deployment** — A Web Application Firewall can block known attack signatures.
4. **Rate Limiting** — Limit requests per IP to detect and block enumeration tools.
5. **IDS/IPS** — Tools like Snort or Suricata can detect and alert on these patterns.
6. **Security Headers** — Implement `Content-Security-Policy` to mitigate XSS.

---

*All testing was performed in a controlled lab environment on intentionally vulnerable software. This report is for educational purposes only.*
