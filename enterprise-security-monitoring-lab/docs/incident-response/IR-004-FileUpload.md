
# Incident Report – IR-004

## Incident Information

| Field | Value |
|-------|-------|
| Incident ID | IR-004 |
| Incident Name | Unrestricted File Upload |
| Severity | High |
| Detection Source | Splunk Enterprise |
| Date | 2026-07-06 |

---

# Executive Summary

A PHP web shell was uploaded through the DVWA File Upload vulnerability after correcting filesystem permissions and SELinux context on the upload directory.

Apache access logs recorded the upload request and Splunk successfully detected the activity.

---

# MITRE ATT&CK Mapping

| Technique | ID |
|-----------|----|
| Web Shell | T1505.003 |

---

# Source Information

Source IP

192.168.52.10

Target

DVWA File Upload Module

---

# Timeline

| Time | Event |
|------|-------|
| Initial upload failed |
| Upload directory permissions corrected |
| SELinux context updated |
| PHP shell uploaded successfully |
| Splunk detected upload request |

---

# Evidence

Uploaded File

```
shell.php
```

Apache Log

```
GET /dvwa/vulnerabilities/upload/
```

---

# Indicators of Compromise

- PHP file upload
- Source IP 192.168.52.10
- Access to upload directory

---

# Analyst Investigation

Initial testing identified incorrect filesystem permissions preventing uploads.

After assigning the appropriate Apache ownership and SELinux context (`httpd_sys_rw_content_t`), file uploads succeeded as expected.

The uploaded PHP file demonstrated how unrestricted file uploads could lead to remote code execution.

---

# Impact

An attacker could upload malicious scripts, establish persistence through a web shell, and execute arbitrary code on the web server.

---

# Recommendations

- Restrict executable uploads
- Validate MIME types
- Store uploads outside the web root
- Disable PHP execution within upload directories
- Deploy antivirus scanning

---

# Incident Status

Closed (Lab Validation)