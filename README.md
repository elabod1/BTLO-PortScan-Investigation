# 🛡️ Incident Report: Local to Local Port Scanning & Web Compromise

## 📌 1. Executive Summary
The SOC received an alert for "Local to Local Port Scanning" from an internal IP (`10.251.96.4`) targeting another internal system (`10.251.96.5`). 
After analyzing the provided PCAP file, it was determined that this activity was **malicious**. The attacker performed reconnaissance, identified open ports, exploited a web application, extracted database credentials, uploaded a web shell, and gained interactive command-line access to the target server.

## 🎯 2. Attack Timeline (Cyber Kill Chain)
1. **Reconnaissance (Port Scanning):** The attacker (`10.251.96.4`) scanned the target (`10.251.96.5`) using a SYN Scan technique. Open ports discovered: **80 (HTTP)** and **22 (SSH)**.
2. **Enumeration:** The attacker used **Gobuster (v3.0.1)** to brute-force hidden directories and files on the web server.
3. **Information Disclosure:** The attacker discovered a highly sensitive file, `info.php`, which exposed the server's internal configuration (PHP version, OS details, and document root paths).
4. **Exploitation (SQL Injection):** The attacker used **sqlmap (v1.4.7)** to perform an SQL Injection attack against the login page, successfully extracting database credentials.
5. **Initial Access (Web Shell Upload):** The attacker used the `upload.php` page to upload a malicious Web Shell.
6. **Command & Control (C2):** The attacker interacted with the Web Shell using the parameter `cmd`, gaining a reverse shell and executing system commands (e.g., `whoami`, `ls`).

## 🔍 3. Technical Details & Evidence

### A. Port Scanning
- **Attacker IP:** `10.251.96.4`
- **Target IP:** `10.251.96.5`
- **Technique:** SYN Scan (Half-Open).
- **Open Ports:** 80 (HTTP), 22 (SSH).

### B. Web Enumeration (Gobuster)
- **Tool Used:** `gobuster/3.0.1`
- **Evidence:** Discovered hidden paths including `/info.php` and `/login.php`.

### C. SQL Injection (sqlmap)
- **Tool Used:** `sqlmap/1.4.7`
- **Compromised Data:** The server leaked database credentials: `root:bobthe@localhost`.

### D. Web Shell Upload & Execution
- **Vulnerable Endpoint:** `upload.php`
- **Executed Parameter:** `cmd`
- **Evidence:** The attacker executed system commands as `www-data`.

## 🚨 4. Indicators of Compromise (IOCs)
| Type | Value |
| :--- | :--- |
| **Attacker IP** | `10.251.96.4` |
| **Target IP** | `10.251.96.5` |
| **Malicious Files** | `info.php`, Web Shell in `/uploads/` |
| **User-Agent** | `gobuster/3.0.1`, `sqlmap/1.4.7` |
| **Compromised Creds** | Username: `root`, Password: `bobthe` |
| **External C2 IPs** | `34.122.121.32`, `35.224.170.84` |

## 🛡️ 5. Recommendations
1. **Isolate** the compromised server (`10.251.96.5`) immediately.
2. **Patch** the SQL Injection vulnerability in `login.php`.
3. **Remove** sensitive files like `info.php`.
4. **Restrict** file uploads on `upload.php`.
5. **Change** all database and system passwords.

---
*Report generated based on PCAP analysis.*
