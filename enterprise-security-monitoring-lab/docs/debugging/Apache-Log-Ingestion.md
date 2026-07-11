
# Apache Log Ingestion Troubleshooting

## Overview

This document describes the troubleshooting process used to resolve a log ingestion issue where Apache HTTP Server continued generating log events, but Splunk Enterprise stopped displaying newly generated events.

Because Apache access logs were the primary data source for detections, threat hunting, correlation searches, and dashboards, resolving this issue was essential to restoring the functionality of the Enterprise Security Monitoring Lab.

---

# Environment

| Component | Value |
|----------|-------|
| Operating System | Red Hat Enterprise Linux 9 |
| SIEM | Splunk Enterprise |
| Web Server | Apache HTTP Server |
| Log Source | Apache access_log and error_log |
| Target Application | DVWA |

---

# Problem Statement

Apache continued writing new events to both the access and error logs; however, Splunk stopped ingesting newly generated events.

Previously indexed events remained searchable, but recent web activity did not appear in Splunk searches.

This prevented validation of attack simulations and temporarily interrupted detection engineering and threat hunting activities.

---

# Symptoms

The following symptoms were observed:

- Apache generated new log entries successfully.
- New HTTP requests appeared in the Apache access log.
- Splunk searches returned only older events.
- Dashboards and reports no longer reflected current web activity.
- Attack simulations generated traffic but produced no corresponding events in Splunk.

Apache logging was verified using:

```bash
sudo tail -f /var/log/httpd/access_log
```

Apache continued writing events correctly.

---

# Root Cause Analysis

Because Apache was successfully generating logs, the issue was determined to be related to Splunk's ability to access and monitor the log files rather than Apache itself.

Potential causes considered included:

- Incorrect monitor configuration
- Disabled Splunk input
- Incorrect index
- Incorrect sourcetype
- File permission issues
- Directory permission issues

---

# Investigation Process

## Step 1 – Verify Apache Log Generation

Confirmed Apache continued writing new events.

```bash
sudo tail -f /var/log/httpd/access_log
```

Result:

Apache logging was functioning normally.

---

## Step 2 – Verify Splunk Monitor Configuration

Reviewed the Splunk input configuration.

```bash
sudo cat /opt/splunk/etc/apps/search/local/inputs.conf
```

Configuration:

```ini
[monitor:///var/log/httpd/access_log]
disabled = false
host = rhel-siem
index = main
sourcetype = apache_access

[monitor:///var/log/httpd/error_log]
disabled = false
host = rhel-siem
index = main
sourcetype = apache_error
```

Result:

Both Apache log files were configured correctly.

---

## Step 3 – Verify Directory Permissions

Reviewed permissions on the Apache log directory.

```bash
ls -ld /var/log/httpd
```

Output:

```text
drwx------ root root
```

Result:

The directory was accessible only by the root user.

The Splunk service account could not enter the directory to continuously monitor the log files.

---

# Root Cause

The Splunk service account lacked sufficient permissions to access the `/var/log/httpd` directory.

Although Apache continued writing new events, Splunk could not read the updated log files because the service account did not have execute permission on the directory or read permission on the log files.

---

# Resolution

Access was granted using Linux Access Control Lists (ACLs).

Directory permission:

```bash
sudo setfacl -m u:splunk:rx /var/log/httpd
```

Log file permissions:

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

Generated a new DVWA request.

Executed the following search:

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/brute"
```

Result:

New Apache events appeared immediately within Splunk.

This confirmed that log ingestion had resumed successfully.

*(Insert screenshot here)*

---

# Impact

Resolving the log ingestion issue restored:

- Real-time Apache log monitoring
- Detection searches
- Scheduled alerts
- Threat hunting
- Correlation searches
- Dashboard updates
- Incident investigations

Without resolving this issue, the remainder of the Enterprise Security Monitoring Lab would not have functioned correctly.

---

# Lessons Learned

This troubleshooting exercise reinforced the importance of validating each layer of the logging pipeline.

A successful log ingestion workflow requires:

1. The application must generate logs.
2. Splunk must monitor the correct files.
3. The Splunk service account must have permission to access those files.

A failure at any point in the pipeline prevents security events from reaching the SIEM.

---

# Best Practices

- Verify application log generation before investigating Splunk.
- Review `inputs.conf` when log ingestion stops.
- Validate Linux directory and file permissions.
- Use ACLs instead of modifying ownership when granting Splunk access.
- Restart Splunk after significant input or permission changes.
- Confirm ingestion by generating new events rather than relying on historical data.