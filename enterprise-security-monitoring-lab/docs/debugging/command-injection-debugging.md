
# Command Injection Troubleshooting

## Overview

This document describes the troubleshooting process used to resolve an issue encountered while testing the Command Injection module within Damn Vulnerable Web Application (DVWA).

Although the module loaded successfully, submitted operating system commands did not return the expected output. Because command injection was one of the simulated attacks used throughout the Enterprise Security Monitoring Lab, resolving this issue was necessary before continuing detection engineering, threat hunting, and incident response activities.

---

# Environment

| Component | Value |
|----------|-------|
| Operating System | Red Hat Enterprise Linux 9 |
| Web Server | Apache HTTP Server |
| Vulnerable Application | DVWA |
| Database | MariaDB |
| SIEM | Splunk Enterprise |
| Browser | Firefox |

---

# Problem Statement

The DVWA Command Injection module accepted user input, but executing commands such as:

```text
whoami
hostname
pwd
id
```

did not produce any output.

Other DVWA modules, including:

- Brute Force
- SQL Injection
- File Upload

were functioning correctly.

---

# Symptoms

The following behavior was observed:

- DVWA login worked correctly.
- Other vulnerability modules operated normally.
- The Command Injection page loaded successfully.
- Submitted commands returned no output.
- Apache continued processing requests without errors visible to the user.

---

# Root Cause Analysis

Because only one DVWA module was affected, infrastructure and networking issues were ruled out early in the investigation.

Potential causes considered included:

- Apache configuration
- PHP configuration
- DVWA security level
- Missing system utilities
- File permissions
- SELinux restrictions
- Application configuration

---

# Investigation Process

## Step 1 – Verify Apache Service

Confirmed Apache was operational.

```bash
sudo systemctl status httpd
```

Result:

Apache was running normally.

---

## Step 2 – Verify DVWA Functionality

Tested additional DVWA modules.

Confirmed:

- Brute Force
- SQL Injection
- File Upload

were all functioning correctly.

This isolated the issue to the Command Injection module.

---

## Step 3 – Review Apache Error Logs

Reviewed Apache error logs while executing commands.

```bash
sudo tail -50 /var/log/httpd/error_log
```

Result:

No critical Apache failures were observed.

---

## Step 4 – Verify DVWA Configuration

Reviewed the DVWA configuration and confirmed the application had been configured correctly.

Verified:

- Database connectivity
- PHP functionality
- Application deployment

---

## Step 5 – Review System Configuration

Verified the operating system environment and investigated whether Linux permissions or security controls were preventing command execution.

The investigation included:

- File permissions
- Apache execution context
- SELinux considerations

---

# Resolution

The issue was resolved after reviewing the application configuration and correcting the underlying condition preventing the Command Injection module from executing commands successfully.

Following the correction:

- Commands executed successfully.
- Expected output was displayed.
- Apache processed requests normally.
- Command execution events appeared in the Apache access log.

---

# Validation

Executed the following commands through the DVWA Command Injection module:

```text
whoami
hostname
pwd
id
```

Each command returned the expected output.

Splunk successfully ingested the corresponding Apache log events, allowing command injection detections and threat hunting searches to function as intended.

---

# Impact

Resolving this issue enabled:

- Command Injection attack simulations
- Detection engineering validation
- Threat hunting exercises
- Correlation searches
- Incident investigations
- MITRE ATT&CK mapping for Execution (T1059)

The Command Injection module became fully operational and integrated into the overall security monitoring workflow.

---

# Lessons Learned

When troubleshooting web application functionality, it is important to isolate the affected component before investigating infrastructure.

Because the remaining DVWA modules functioned correctly, attention shifted away from networking and web server configuration toward application-specific behavior.

A structured troubleshooting methodology significantly reduced the time required to identify and resolve the issue.

---

# Best Practices

- Verify infrastructure before troubleshooting application logic.
- Compare behavior across multiple application modules.
- Review Apache error logs during testing.
- Validate application configuration before modifying server settings.
- Confirm functionality after each change to isolate the successful fix.
- Document the troubleshooting process for future reference and repeatability.