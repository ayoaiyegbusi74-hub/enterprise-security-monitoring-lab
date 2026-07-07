
# Incident Report – IR-001

## Incident Information

| Field | Value |
|-------|-------|
| Incident ID | IR-001 |
| Incident Name | DVWA Brute Force Attack |
| Severity | Medium |
| Detection Source | Splunk Enterprise |
| Date | 2026-07-06 |

---

# Executive Summary

A brute force attack was performed against the DVWA login page from the Kali Linux attacker machine. Multiple authentication attempts were observed in the Apache access logs and successfully detected by Splunk Enterprise.

The activity simulated an attacker attempting to gain unauthorized access by repeatedly guessing valid credentials.

---

# MITRE ATT&CK Mapping

| Technique | ID |
|-----------|----|
| Brute Force | T1110 |

---

# Source Information

Source IP

192.168.52.10

Target

RHEL DVWA Web Server

Application

DVWA

---

# Timeline

| Time | Event |
|------|-------|
| 10:05 | Attacker accessed DVWA login page |
| 10:06 | Multiple authentication attempts submitted |
| 10:06 | Apache logged HTTP requests |
| 10:07 | Splunk detection identified brute force activity |

---

# Evidence

Evidence collected included:

- Apache access logs
- Splunk detection report
- Splunk dashboard
- Browser screenshots

Example Log

GET /dvwa/vulnerabilities/brute/

POST /dvwa/login.php

---

# Indicators of Compromise (IOCs)

- Source IP: 192.168.52.10
- Multiple repeated login requests
- High volume of HTTP GET/POST requests

---

# Analyst Investigation

The Apache access log was reviewed to confirm repeated login attempts originating from the Kali Linux attacker machine.

Splunk successfully parsed the log fields and identified repeated requests to the DVWA brute force module.

No additional attacker IP addresses were observed.

---

# Impact

If performed against a production system, this attack could result in unauthorized account access through password guessing.

---

# Recommendations

- Enable Multi-Factor Authentication (MFA)
- Configure account lockout policies
- Deploy Fail2Ban
- Implement rate limiting
- Monitor authentication failures continuously

---

# Incident Status

Closed (Lab Validation)