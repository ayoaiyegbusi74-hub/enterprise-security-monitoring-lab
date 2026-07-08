
# Incident Investigation

## Executive Summary

This investigation documents a simulated multi-stage cyber attack against a Damn Vulnerable Web Application (DVWA) hosted on an Apache web server running on Red Hat Enterprise Linux (RHEL 9). Security events were collected from Apache access logs and analyzed using Splunk Enterprise.

The investigation identified an external attacker originating from the Kali Linux attack workstation (192.168.52.10). Correlation searches reconstructed the complete attack lifecycle, beginning with reconnaissance using the Nmap Scripting Engine and progressing through brute-force authentication attempts, SQL injection, command injection, and malicious file upload activity.

Each stage of the attack was correlated and mapped to the MITRE ATT&CK framework, providing a structured view of the attack progression and demonstrating how individual events combine to form a complete incident.

---

# Incident Overview

| Category | Value |
|----------|-------|
| Incident Type | Multi-Stage Web Application Attack |
| Severity | High |
| Investigation Status | Completed |
| SIEM Platform | Splunk Enterprise |
| Data Source | Apache access_log |
| Target System | RHEL 9 Apache Web Server (DVWA) |
| Attacker System | Kali Linux |
| Attacker IP | 192.168.52.10 |

---

# Investigation Objectives

The objectives of this investigation were to:

- Identify malicious web activity targeting the DVWA application.
- Reconstruct the complete attack timeline.
- Correlate multiple attack stages into a single investigation.
- Map attacker behavior to the MITRE ATT&CK framework.
- Document Indicators of Compromise (IOCs).
- Demonstrate enterprise SOC investigation methodology using Splunk Enterprise.

---

# Attack Timeline

| Timeframe | Attack Stage | Description |
|-----------|--------------|-------------|
| June 30, 2026 | Reconnaissance | Nmap Scripting Engine scanned the target web server and requested multiple application resources. |
| July 1, 2026 | Credential Access | Multiple brute-force login attempts were observed against the DVWA authentication module. |
| July 1, 2026 | Initial Access | SQL injection payloads were submitted against the vulnerable SQL module. |
| July 1, 2026 | Execution | Command injection payloads were executed against the DVWA command execution module. |
| July 2, 2026 | Persistence | A PHP web shell was uploaded through the DVWA file upload functionality and subsequently accessed. |

---

# Attack Progression

```text
Attacker (192.168.52.10)
          │
          ▼
Reconnaissance
(Nmap NSE Scan)
          │
          ▼
Credential Access
(Brute Force)
          │
          ▼
Initial Access
(SQL Injection)
          │
          ▼
Execution
(Command Injection)
          │
          ▼
Persistence
(PHP Web Shell Upload)
```

---

# Investigation Findings

## Phase 1 – Reconnaissance

The attacker initiated reconnaissance using the Nmap Scripting Engine. Apache logs recorded requests for several known application paths and default resources, including:

- /HNAP1
- /sdk
- /evox/about
- Additional Nmap-generated probes

These requests generated HTTP 404 responses, indicating the attacker was identifying available services and potential attack surfaces.

Evidence:

- User-Agent:
  ```
  Mozilla/5.0 (compatible; Nmap Scripting Engine)
  ```

MITRE ATT&CK

- Tactic: Reconnaissance
- Technique: T1595 – Active Scanning

---

## Phase 2 – Credential Access

Following reconnaissance, the attacker targeted the DVWA brute-force module.

Observed activity included multiple username and password combinations submitted against the login interface.

MITRE ATT&CK

- Tactic: Credential Access
- Technique: T1110 – Brute Force

---

## Phase 3 – Initial Access

The attacker attempted SQL Injection against the vulnerable SQL module.

Observed requests included SQL payloads submitted through vulnerable application parameters.

MITRE ATT&CK

- Tactic: Initial Access
- Technique: T1190 – Exploit Public-Facing Application

---

## Phase 4 – Execution

After identifying vulnerable functionality, the attacker executed operating system commands through the DVWA command injection page.

Apache logs recorded multiple POST requests to:

```
/dvwa/vulnerabilities/exec/
```

MITRE ATT&CK

- Tactic: Execution
- Technique: T1059 – Command and Scripting Interpreter

---

## Phase 5 – Persistence

The attacker uploaded a PHP web shell through the vulnerable upload functionality.

Subsequent requests confirmed successful access to the uploaded file.

MITRE ATT&CK

- Tactic: Persistence
- Technique: T1505.003 – Web Shell

---

# Correlation Searches Used

The investigation leveraged multiple Splunk correlation searches to reconstruct the attack.

| Correlation Search | Purpose |
|--------------------|---------|
| CORR - Full Attack Chain Timeline | Reconstructed attacker activity chronologically. |
| CORR - Incident Summary | Summarized attacker activity and investigation details. |
| CORR - Attack Session | Grouped related attack events into a single investigation. |
| CORR - MITRE Tactic Distribution | Categorized activity by MITRE ATT&CK tactic. |
| CORR - Executive Attack Overview | Produced a high-level incident summary for reporting. |

---

# Threat Hunting Support

Threat hunting searches were used to validate and expand the investigation.

Threat hunts included:

- Attacker Activity
- Multi-Stage Attack
- User-Agent Analysis
- HTTP 500 Errors
- HTTP Method Analysis
- Most Active Source IPs
- Most Targeted URIs
- Attack Timeline
- Attack Type Distribution
- Abnormal Status Codes

These hunts provided additional context beyond traditional detections and confirmed attacker behavior throughout the investigation.

---

# Indicators of Compromise

Primary IOC

- Source IP: 192.168.52.10

Observed Techniques

- Nmap Reconnaissance
- Brute Force
- SQL Injection
- Command Injection
- File Upload
- PHP Web Shell Access

Observed HTTP Methods

- GET
- POST

Observed HTTP Status Codes

- 200
- 302
- 304
- 404
- 500

Observed User-Agents

- Firefox
- Nmap Scripting Engine
- curl

---

# MITRE ATT&CK Mapping

| Tactic | Technique |
|---------|-----------|
| Reconnaissance | T1595 – Active Scanning |
| Credential Access | T1110 – Brute Force |
| Initial Access | T1190 – Exploit Public-Facing Application |
| Execution | T1059 – Command and Scripting Interpreter |
| Persistence | T1505.003 – Web Shell |

---

# Lessons Learned

This project demonstrated the importance of correlating multiple security events rather than relying on isolated detections.

Key observations include:

- Reconnaissance activity often precedes exploitation attempts.
- Correlation searches provide significantly more context than individual alerts.
- Threat hunting complements automated detections by validating attacker behavior.
- MITRE ATT&CK mapping improves investigation consistency and communication.
- Apache web logs provide valuable forensic evidence for reconstructing web application attacks.
- Splunk Enterprise enables effective detection engineering, threat hunting, correlation, and incident investigation using a single centralized platform.

---

# Conclusion

This investigation successfully reconstructed a complete multi-stage attack against a vulnerable web application using Splunk Enterprise.

By combining detection engineering, threat hunting, correlation searches, and MITRE ATT&CK mapping, the investigation demonstrated a realistic SOC workflow for identifying, analyzing, and documenting malicious web activity.

The completed investigation provides a repeatable methodology for future incident response activities and serves as a practical demonstration of enterprise security monitoring and web application attack analysis.