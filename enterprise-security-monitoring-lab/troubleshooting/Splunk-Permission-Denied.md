
---

# Splunk Permission Denied - Linux ACL Fix

## Issue

Splunk stopped ingesting Apache logs because it could not access the log directory.

---

## Error

Checking the directory showed:

```
drwx------ root root /var/log/httpd
```

Trying to access the file as a normal user returned:

```
Permission denied
```

---

## Root Cause

The Apache log directory only allowed the **root** user to enter.

Although the log file itself was readable, Splunk first needed permission to access the directory.

---

## Resolution

Grant directory access:

```bash
sudo setfacl -m u:splunk:rx /var/log/httpd
```

Grant file access:

```bash
sudo setfacl -m u:splunk:r /var/log/httpd/access_log
sudo setfacl -m u:splunk:r /var/log/httpd/error_log
```

Restart Splunk:

```bash
sudo /opt/splunk/bin/splunk restart
```

---

## Verification

```
sudo getfacl /var/log/httpd
```

result:

```
user:splunk:r-x
```

```
sudo getfacl /var/log/httpd/access_log
```

result:

```
user:splunk:r--
```

After generating new web traffic, Splunk successfully indexed the latest Apache events.

---

## Lesson Learned

When troubleshooting Linux log ingestion:

- File permissions matter.
- Directory permissions matter.
- ACLs are often a better solution than changing ownership or making logs world-readable.

---