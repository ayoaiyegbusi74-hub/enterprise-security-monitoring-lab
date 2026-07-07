
# Incident Report – IR-002

## Incident Information

| Field | Value |
|-------|-------|
| Incident ID | IR-002 |
| Incident Name | SQL Injection Attempt |
| Severity | High |
| Detection Source | Splunk Enterprise |
| Date | 2026-07-06 |

---

# Executive Summary

SQL injection payloads were submitted through the DVWA SQL Injection module to simulate an attacker attempting to retrieve unauthorized database information.

Apache access logs recorded the requests and Splunk successfully detected the activity.

---

# MITRE ATT&CK Mapping

| Technique | ID |
|-----------|----|
| Exploit Public-Facing Application | T1190 |

---

# Source Information

Source IP

192.168.52.10

Target

DVWA SQL Injection Module

---

# Timeline

| Time | Event |
|------|-------|
| User accessed SQL Injection page |
| SQL payload submitted |
| Apache logged request |
| Splunk indexed event |
| Detection rule triggered |

---

# Evidence

- Apache Access Log
- Splunk Search Results
- Dashboard Visualization

Example Payload

```
' OR '1'='1
```

---

# Indicators of Compromise

- SQL keywords observed
- Suspicious URI parameters
- Source IP 192.168.52.10

---

# Analyst Investigation

Apache access logs confirmed SQL injection payloads submitted against the vulnerable web application.

Splunk detection identified suspicious SQL keywords contained within HTTP requests.

---

# Impact

A successful SQL Injection attack could expose sensitive database information or allow unauthorized database manipulation.

---

# Recommendations

- Use parameterized SQL queries
- Validate user input
- Implement a Web Application Firewall (WAF)
- Conduct secure code reviews

---

# Incident Status

Closed (Lab Validation)