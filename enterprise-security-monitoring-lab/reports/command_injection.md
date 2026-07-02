
# Command Injection Detection

## Objective

Demonstrate a Command Injection attack against DVWA, observe the resulting web server activity, and collect evidence in Apache and Splunk.

---

# MITRE ATT&CK

Technique:

- T1059 — Command and Scripting Interpreter

---

# Attack Flow

```
Attacker (Kali)
        │
        ▼
DVWA Command Injection Page
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

# Vulnerability

DVWA's Command Injection module executes operating system commands supplied by user input using PHP's `shell_exec()` function.

At the Low security level, the application executes:

```php
$cmd = shell_exec('ping -c 4 ' . $target);
```

Unsanitized user input allows an attacker to execute arbitrary operating system commands.

---

# Initial Issue

During testing, the default DVWA implementation attempted to execute:

```
ping -c 4 <user_input>
```

However, no output appeared in the web application even though the request was successfully processed.

The application simply reloaded without displaying any command output.

---

# Temporary Modification

To verify command execution, the vulnerable source file was temporarily modified.

Original:

```php
$cmd = shell_exec('ping -c 4 ' . $target);
```

Modified:

```php
$cmd = shell_exec($target . ' 2>&1');
```

This modification was performed strictly for demonstration purposes to validate command execution.

---

# Test Payload

```
whoami
```

---

# Result

The application successfully returned:

```
apache
```

confirming that arbitrary operating system commands were executed by the Apache service account.

---

# Apache Evidence

Apache access log recorded the request:

```
POST /dvwa/vulnerabilities/exec/ HTTP/1.1
```

indicating successful interaction with the vulnerable endpoint.

---

# Splunk Search

```
index=main sourcetype=apache_access
| search exec
```

or

```
index=main "exec"
```

---

# Detection Opportunities

A SOC analyst should investigate:

- Access to `/dvwa/vulnerabilities/exec/`
- High volume POST requests
- Requests from unexpected source IP addresses
- Command Injection attempts
- Repeated exploitation activity

---

# Outcome

Command execution was successfully demonstrated using the `whoami` command.

Apache successfully logged the HTTP requests.

Splunk ingestion of these specific events requires additional investigation.