
# MITRE ATT&CK Mapping

## Overview

This document maps the simulated attacks performed against the DVWA environment to the MITRE ATT&CK Enterprise Framework. Each attack observed during the investigation was analyzed to determine the most appropriate ATT&CK tactic and technique based on attacker behavior.

Mapping attack activity to MITRE ATT&CK provides a standardized method for classifying adversary techniques, improving detection engineering, threat hunting, and incident response.

---

# ATT&CK Mapping Summary

| Attack | Tactic | Technique | ATT&CK ID |
|---------|--------|-----------|-----------|
| Nmap Scan | Reconnaissance | Active Scanning | T1595 |
| Brute Force | Credential Access | Brute Force | T1110 |
| SQL Injection | Initial Access | Exploit Public-Facing Application | T1190 |
| Command Injection | Execution | Command and Scripting Interpreter | T1059 |
| File Upload (PHP Web Shell) | Persistence | Web Shell | T1505.003 |

---

# Attack 1 – Reconnaissance

## Attack

Nmap Scripting Engine Scan

## Objective

Identify exposed services, web resources, and potential vulnerabilities before exploitation.

## Evidence

Observed Apache requests included:

- /HNAP1
- /sdk
- /evox/about
- Additional web enumeration requests

Observed User-Agent:

```
Mozilla/5.0 (compatible; Nmap Scripting Engine)
```

## MITRE ATT&CK Mapping

| Category | Value |
|----------|-------|
| Tactic | Reconnaissance |
| Technique | Active Scanning |
| ATT&CK ID | T1595 |

## Why This Mapping?

The attacker actively probed the target web application to discover accessible resources and identify potential attack surfaces. This behavior aligns with MITRE ATT&CK Technique T1595 (Active Scanning), which describes gathering information about a target prior to exploitation.

---

# Attack 2 – Brute Force

## Attack

Credential Brute Force

## Objective

Attempt to gain access using multiple username and password combinations.

## Evidence

Target URI:

```
/dvwa/vulnerabilities/brute/
```

## MITRE ATT&CK Mapping

| Category | Value |
|----------|-------|
| Tactic | Credential Access |
| Technique | Brute Force |
| ATT&CK ID | T1110 |

## Why This Mapping?

The attacker repeatedly attempted authentication against the DVWA login interface using multiple credential combinations. This behavior directly matches MITRE ATT&CK Technique T1110 (Brute Force).

---

# Attack 3 – SQL Injection

## Attack

SQL Injection

## Objective

Exploit vulnerable database queries to manipulate backend SQL statements.

## Evidence

Target URI:

```
/dvwa/vulnerabilities/sqli/
```

## MITRE ATT&CK Mapping

| Category | Value |
|----------|-------|
| Tactic | Initial Access |
| Technique | Exploit Public-Facing Application |
| ATT&CK ID | T1190 |

## Why This Mapping?

The attacker exploited a vulnerable web application endpoint by injecting malicious SQL statements. This technique is consistent with MITRE ATT&CK T1190 (Exploit Public-Facing Application).

---

# Attack 4 – Command Injection

## Attack

Command Injection

## Objective

Execute operating system commands through vulnerable application functionality.

## Evidence

Target URI:

```
/dvwa/vulnerabilities/exec/
```

## MITRE ATT&CK Mapping

| Category | Value |
|----------|-------|
| Tactic | Execution |
| Technique | Command and Scripting Interpreter |
| ATT&CK ID | T1059 |

## Why This Mapping?

The attacker leveraged application input to execute operating system commands on the server. This aligns with MITRE ATT&CK T1059 (Command and Scripting Interpreter).

---

# Attack 5 – File Upload

## Attack

Malicious File Upload (PHP Web Shell)

## Objective

Upload executable content to establish persistent access.

## Evidence

Target URI:

```
/dvwa/vulnerabilities/upload/
```

## MITRE ATT&CK Mapping

| Category | Value |
|----------|-------|
| Tactic | Persistence |
| Technique | Web Shell |
| ATT&CK ID | T1505.003 |

## Why This Mapping?

The attacker uploaded a PHP file capable of executing server-side code. Web shells are commonly used to maintain persistent access to compromised web servers, matching MITRE ATT&CK T1505.003 (Web Shell).

---

# Attack Lifecycle

The observed attack followed a logical progression consistent with common web application compromises.

```text
Reconnaissance
        │
        ▼
Credential Access
        │
        ▼
Initial Access
        │
        ▼
Execution
        │
        ▼
Persistence
```

| Stage | ATT&CK Tactic | ATT&CK Technique |
|--------|---------------|------------------|
| Reconnaissance | Reconnaissance | T1595 |
| Brute Force | Credential Access | T1110 |
| SQL Injection | Initial Access | T1190 |
| Command Injection | Execution | T1059 |
| File Upload | Persistence | T1505.003 |

---

# Detection Coverage

The mapped ATT&CK techniques were detected and investigated using multiple capabilities within Splunk Enterprise.

| Capability | Purpose |
|------------|---------|
| Detection Rules | Generated alerts for known attack patterns. |
| Alerts | Notified analysts of suspicious activity. |
| Reports | Summarized attack events. |
| Threat Hunts | Proactively searched for malicious behavior. |
| Correlation Searches | Reconstructed the complete attack chain. |
| Dashboards | Visualized attack trends and investigation metrics. |

---

# Defensive Recommendations

Based on the observed techniques, the following defensive controls are recommended:

| ATT&CK Technique | Recommended Mitigation |
|------------------|------------------------|
| T1595 – Active Scanning | Deploy a Web Application Firewall (WAF), restrict unnecessary services, and monitor reconnaissance patterns. |
| T1110 – Brute Force | Enforce strong password policies, implement account lockout thresholds, and enable MFA where applicable. |
| T1190 – Exploit Public-Facing Application | Validate user input, use parameterized SQL queries, and keep applications fully patched. |
| T1059 – Command and Scripting Interpreter | Sanitize input, avoid direct command execution from web applications, and restrict shell access. |
| T1505.003 – Web Shell | Restrict executable uploads, validate file types, scan uploaded content, and monitor web directories for unauthorized files. |

---

# Conclusion

The simulated attack successfully demonstrated how MITRE ATT&CK can be used to classify and understand adversary behavior throughout the attack lifecycle.

By mapping each attack to its corresponding ATT&CK tactic and technique, the investigation became more structured, detection coverage was easier to evaluate, and incident response activities were aligned with an industry-standard framework.

This mapping also provides a repeatable methodology for future threat hunting, detection engineering, and SOC investigations.