
# Splunk Permission Denied – Linux ACL Fix

## Overview

This document describes the troubleshooting process used to resolve a Linux permission issue that prevented Splunk Enterprise from accessing Apache log files.

Although Apache continued generating log events, the Splunk service account could not access the Apache log directory due to restrictive Linux permissions. This prevented real-time log ingestion and interrupted security monitoring within the Enterprise Security Monitoring Lab.

---

# Environment

| Component | Value |
|----------|-------|
| Operating System | Red Hat Enterprise Linux 9 |
| SIEM | Splunk Enterprise |
| Web Server | Apache HTTP Server |
| Log Directory | /var/log/httpd |
| Log Files | access_log, error_log |

---

# Problem Statement

Splunk stopped ingesting Apache log events even though Apache was actively generating new log entries.

Investigation revealed that the Splunk service account could not access the Apache log directory because of restrictive Linux file permissions.

---

# Symptoms

The following symptoms were observed:

- Apache continued generating log events.
- Splunk searches returned only historical events.
- Newly generated Apache requests never appeared in Splunk.
- The Splunk service account could not list the Apache log directory.

Verification:

```bash
sudo -u splunk ls -l /var/log/httpd
```

Output:

```text
ls: cannot open directory '/var/log/httpd': Permission denied
```

This confirmed that the issue was related to Linux permissions rather than Splunk configuration.

---

# Root Cause Analysis

Apache log files were owned by the `root` user, and the `/var/log/httpd` directory had restrictive permissions.

Directory permissions:

```bash
ls -ld /var/log/httpd
```

Output:

```text
drwx------ root root
```

Although the Splunk service was running correctly, it could not traverse the directory to monitor the log files.

---

# Investigation Process

## Step 1 – Verify Splunk Service

Confirmed Splunk Enterprise was running.

```bash
sudo /opt/splunk/bin/splunk status
```

Result:

Splunk service was operational.

---

## Step 2 – Verify Apache Log Generation

Confirmed Apache continued writing events.

```bash
sudo tail -f /var/log/httpd/access_log
```

Result:

Apache logging functioned normally.

---

## Step 3 – Test Splunk Access

Executed:

```bash
sudo -u splunk ls -l /var/log/httpd
```

Result:

```text
Permission denied
```

This isolated the issue to Linux file permissions.

---

## Step 4 – Review Directory Permissions

Checked directory permissions.

```bash
ls -ld /var/log/httpd
```

Confirmed the directory allowed access only to the root user.

---

# Resolution

Rather than changing ownership or weakening system permissions, Linux Access Control Lists (ACLs) were used to grant the Splunk service account only the permissions it required.

Granted directory access:

```bash
sudo setfacl -m u:splunk:rx /var/log/httpd
```

Granted read access to Apache logs:

```bash
sudo setfacl -m u:splunk:r /var/log/httpd/access_log
sudo setfacl -m u:splunk:r /var/log/httpd/error_log
```

Restarted Splunk:

```bash
sudo /opt/splunk/bin/splunk restart
```

---

# Validation

Verified directory access:

```bash
sudo -u splunk ls -l /var/log/httpd
```

Result:

The Splunk service account successfully listed the directory contents.

Generated a new request against DVWA and confirmed the event appeared in Splunk immediately.

---

# Impact

Resolving the permission issue restored:

- Continuous Apache log ingestion
- Detection searches
- Scheduled alerts
- Threat hunting
- Correlation searches
- Dashboard updates
- Incident investigations

The SIEM resumed processing real-time security events.

---

# Lessons Learned

Linux file permissions are a common cause of SIEM log ingestion failures.

When troubleshooting log collection:

1. Verify the application is generating logs.
2. Confirm the SIEM service is running.
3. Test access using the SIEM service account.
4. Review directory and file permissions.
5. Apply the principle of least privilege when granting access.

Using ACLs allows administrators to grant precise permissions without changing file ownership or making sensitive log directories broadly accessible.

---

# Best Practices

- Use ACLs instead of changing file ownership whenever possible.
- Test permissions as the service account rather than as root.
- Verify log generation before troubleshooting ingestion.
- Restart Splunk after significant permission changes.
- Validate the fix by generating new events instead of relying on historical logs.
- Follow the principle of least privilege when granting access to security logs.