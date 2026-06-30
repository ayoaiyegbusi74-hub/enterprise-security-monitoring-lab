
---

# Nmap Reconnaissance  
## MITRE ATT&CK

### Tactic  
**Reconnaissance**

### Technique  
**T1595 – Active Scanning**

---

## Attack Flow

        ```text
        Attacker (Kali)
                │
                │
                ▼
        Apache Web Server (RHEL)
                │
                ▼
        Apache Access Log
                │
                ▼
        Splunk Enterprise
                │
                ▼
        SOC Investigation
        ```

---

## What Happened?

From Kali I ran:

```
nmap -sV 192.168.52.20
```

Nmap probed the Apache server with several HTTP requests attempting to identify the software and version.

![alt text](<Screenshot 2026-06-30 105024.png>)

Evidence included requests such as:

- **GET /HNAP1**
- **GET /sdk**
- **GET /evox/about**
- **GET /nmaplowercheck**

User agent observed:

```
Nmap Scripting Engine
```

![alt text](<Screenshot 2026-06-30 104759.png>)

---

## Splunk Search

Run:

```
index=main sourcetype=apache_access
"Nmap Scripting Engine"
```

Result:

![alt text](<Screenshot 2026-06-30 104911.png>)

Requests observed:

- **GET /HNAP1**
- **GET /sdk**
- **GET /evox/about**

---

## Better Detection Query

```
index=main sourcetype=apache_access
"Nmap Scripting Engine"
| stats count by clientip
```

### Result

| Client IP        | Count |
|------------------|-------|
| **192.168.52.10** | 15 |

### Meaning

Host **192.168.52.10** performed multiple reconnaissance requests.

---

## SOC Investigation

Questions an analyst would ask:

- **Who is 192.168.52.10?**
- **Is it an approved vulnerability scanner?**
- **Has it scanned other servers?**
- **Did the scan continue into exploitation?**

This is exactly how analysts investigate.

---

## Incident Report

### Alert  
**Possible Network Reconnaissance**

### Severity  
**Medium**

### Source IP  
**192.168.52.10**

### Evidence  
Multiple HTTP requests associated with  
**Nmap Scripting Engine** detected.

### MITRE  
**T1595 – Active Scanning**

### Recommendation  
Determine whether the scan originated from an authorized administrator.

If unauthorized:

- **Block IP**  
- **Investigate subsequent activity**  
- **Review firewall logs**

---

