
# Apache 500 Internal Server Error Troubleshooting

## Overview

This document describes the troubleshooting process used to resolve an HTTP 500 Internal Server Error encountered while accessing the Damn Vulnerable Web Application (DVWA) hosted on an Apache HTTP Server running on Red Hat Enterprise Linux 9.

The issue prevented access to the DVWA application and temporarily halted all attack simulations until the root cause was identified and resolved.

---

# Environment

| Component | Value |
|----------|-------|
| Operating System | Red Hat Enterprise Linux 9 |
| Web Server | Apache HTTP Server |
| Application | DVWA (Damn Vulnerable Web Application) |
| Database | MariaDB |
| SIEM | Splunk Enterprise |
| Browser | Firefox |

---

# Problem Statement

The Apache web server was reachable, but navigating to the DVWA application resulted in an HTTP 500 Internal Server Error.

Because the application failed to load correctly, attack simulations such as brute force, SQL injection, command injection, and file upload could not be performed.

---

# Symptoms

The following symptoms were observed:

- Apache service was running.
- The Apache test page loaded successfully.
- Accessing `/dvwa/` returned an HTTP 500 Internal Server Error.
- DVWA pages failed to render.
- Attack simulations could not be executed.

---

# Root Cause Analysis

An HTTP 500 response indicates that the request reached the web server successfully, but the server encountered an internal error while processing the request.

Potential causes considered included:

- Apache configuration issues.
- PHP processing errors.
- MariaDB connectivity problems.
- Incorrect DVWA configuration.
- File permission issues.
- SELinux restrictions.

---

# Investigation Process

## Step 1 – Verify Apache Service

Confirmed Apache was running.

```bash
sudo systemctl status httpd
```

Result:

- Apache service was active.
- No startup failures were observed.

---

## Step 2 – Review Apache Error Logs

Examined the Apache error log.

```bash
sudo tail -50 /var/log/httpd/error_log
```

This helped identify server-side errors generated while attempting to access DVWA.

---

## Step 3 – Verify PHP Installation

Confirmed PHP was installed and available to Apache.

```bash
php -v
```

Verified the required PHP modules for DVWA were present.

---

## Step 4 – Verify MariaDB

Confirmed the database service was operational.

```bash
sudo systemctl status mariadb
```

Verified:

- Database service running.
- DVWA database accessible.

---

## Step 5 – Review DVWA Configuration

Reviewed the DVWA configuration file to confirm:

- Database hostname.
- Database username.
- Database password.
- Database name.

Verified configuration matched the MariaDB deployment.

---

## Step 6 – Verify File Permissions

Checked ownership and permissions for the DVWA directory.

```bash
ls -la /var/www/html/
```

Verified Apache could access the application files.

---

## Step 7 – Consider SELinux

Because RHEL enables SELinux by default, SELinux policy restrictions were investigated as a possible cause.

SELinux contexts and permissions were reviewed to ensure Apache could access the required application resources.

---

# Resolution

The issue was resolved after verifying the web application stack and correcting the underlying configuration preventing DVWA from executing correctly.

Once the application configuration was corrected:

- Apache successfully processed PHP requests.
- DVWA loaded normally.
- The login page became accessible.
- All attack modules functioned correctly.

---

# Validation

The following checks confirmed the issue had been resolved:

- Apache served the DVWA login page.
- No additional HTTP 500 responses were observed.
- Brute force module functioned correctly.
- SQL Injection module loaded successfully.
- Command Injection module became accessible.
- File Upload module loaded correctly.
- Attack simulations generated Apache logs for Splunk ingestion.

---

# Impact

Resolving the HTTP 500 error restored the web application and enabled the remainder of the security monitoring project.

This allowed:

- Web application attack simulations.
- Apache log generation.
- Splunk detection engineering.
- Threat hunting.
- Correlation searches.
- Incident investigations.

---

# Lessons Learned

An HTTP 500 Internal Server Error often indicates an application-layer problem rather than a network issue.

A structured troubleshooting methodology should include:

1. Verify the web server.
2. Review application logs.
3. Confirm PHP functionality.
4. Validate database connectivity.
5. Check application configuration.
6. Review file permissions.
7. Investigate SELinux restrictions when using RHEL.

Working through the stack methodically prevents unnecessary troubleshooting and helps isolate the true root cause efficiently.

---

# Best Practices

- Review Apache error logs before making configuration changes.
- Verify every layer of the web application stack individually.
- Validate database connectivity after application deployment.
- Confirm correct file ownership and permissions.
- Consider SELinux policies early when troubleshooting web applications on RHEL.
- Test application functionality after each configuration change to isolate the point of failure.