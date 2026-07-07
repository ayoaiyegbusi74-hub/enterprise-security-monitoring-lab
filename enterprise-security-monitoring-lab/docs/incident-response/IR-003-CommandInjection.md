
# Incident Report – IR-003

## Incident Information

| Field | Value |
|-------|-------|
| Incident ID | IR-003 |
| Incident Name | Command Injection |
| Severity | High |
| Detection Source | Splunk Enterprise |
| Date | 2026-07-06 |

---

# Executive Summary

Command injection testing was performed against the DVWA Command Injection module.

The application successfully executed basic operating system commands such as `whoami`; however, ICMP ping execution failed due to Linux capability restrictions on the `ping` binary rather than an application vulnerability.

Apache successfully logged all HTTP requests associated with the attack.

---

# MITRE ATT&CK Mapping

| Technique | ID |
|-----------|----|
| Command and Scripting Interpreter | T1059 |

---

# Source Information

Source IP

192.168.52.10

Target

DVWA Command Injection Module

---

# Timeline

| Time | Event |
|------|-------|
| Accessed Command Injection page |
| Submitted command payload |
| Apache logged POST request |
| Investigated command execution failure |
| Determined ping capability issue |

---

# Evidence

Commands Executed

```
whoami
id
hostname
ls
```

Observed Behavior

- whoami executed successfully
- id executed successfully
- hostname executed successfully
- ping failed

---

# Indicators of Compromise

- HTTP POST requests
- Source IP 192.168.52.10
- Command execution observed

---

# Analyst Investigation

Extensive troubleshooting confirmed that the DVWA application correctly executed shell commands.

Testing revealed that only the `ping` command failed due to missing Linux capabilities (`cap_net_raw`) within the Apache execution context.

Additional testing demonstrated successful execution of several other operating system commands.

This behavior indicates an operating system capability issue rather than a failure of the DVWA application itself.

---

# Impact

Successful command injection may allow attackers to execute arbitrary operating system commands and potentially compromise the underlying server.

---

# Recommendations

- Eliminate use of shell_exec()
- Validate all user input
- Apply least privilege
- Disable unnecessary command execution
- Monitor abnormal command activity

---

# Incident Status

Closed (Lab Validation)