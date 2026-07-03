
# Splunk Enterprise Installation

## Project

Enterprise Security Monitoring & Vulnerability Lab

---

# Objective

Install Splunk Enterprise on the RHEL server to collect, index, and investigate Apache web server logs generated during DVWA attack simulations.

---

# Environment

| Component | Value |
|------------|--------|
| Platform | RHEL 9 |
| SIEM | Splunk Enterprise |
| Index | main |

---

# Installation

Downloaded Splunk Enterprise.

Installed:

```
sudo rpm -ivh splunk*.rpm
```

Started:

```
sudo /opt/splunk/bin/splunk start --accept-license
```

Enabled boot startup:

```
sudo /opt/splunk/bin/splunk enable boot-start
```

---

# Web Interface

```
http://RHEL-IP:8000
```

---

# Log Sources

Apache Access Log

```
/var/log/httpd/access_log
```

Apache Error Log

```
/var/log/httpd/error_log
```

Linux Secure Log

```
/var/log/secure
```

---

# Inputs

Configured through:

```
Settings

Data Inputs

Files & Directories
```

---

# Sourcetypes

```
apache_access

apache_error

linux_secure
```

---

# Verification

Search

```spl
index=main
```

Apache

```spl
index=main sourcetype=apache_access
```

Errors

```spl
index=main sourcetype=apache_error
```

Secure Log

```spl
index=main sourcetype=linux_secure
```

---

# Attack Detection

Successfully detected:

- SQL Injection
- Stored XSS
- Brute Force
- Command Injection requests
- File Upload

using Apache access logs.

---

# Example Searches

SQL Injection

```spl
index=main "/dvwa/vulnerabilities/sqli/"
```

Stored XSS

```spl
index=main "/dvwa/vulnerabilities/xss_s/"
```

Command Injection

```spl
index=main "/dvwa/vulnerabilities/exec/"
```

File Upload

```spl
index=main "/dvwa/vulnerabilities/upload/"
```

Top Source IPs

```spl
index=main
| stats count by clientip
```

Timeline

```spl
index=main
| timechart count
```

---

# Lessons Learned

Splunk provided centralized visibility into web application attacks through Apache logs, enabling investigation, timeline analysis, IOC collection, and correlation searches without requiring endpoint agents.