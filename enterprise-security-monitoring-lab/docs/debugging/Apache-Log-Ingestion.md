
---

# Apache Log Ingestion Troubleshooting

## Issue

Apache was generating new logs, but Splunk was not displaying the latest events.

---

## Symptoms

Apache access log showed new requests:

```bash
sudo tail -f /var/log/httpd/access_log
```

However, Splunk searches only returned older events.

---

## Investigation

### Step 1

Verified Apache was still writing logs.

```
sudo tail -f /var/log/httpd/access_log
```

Result:

Apache logging was working.

---

### Step 2

Verified Splunk input configuration.

```
sudo cat /opt/splunk/etc/apps/search/local/inputs.conf
```

Result:

```
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

Both log files were configured correctly.

---

### Step 3

Checked Linux permissions.

```
ls -ld /var/log/httpd
```

Result:

```
drwx------ root root
```

The directory was accessible only by root.

---

## Root Cause

Although Apache was writing logs, the Splunk service account did not have permission to enter the `/var/log/httpd` directory and continuously monitor the log files.

---

## Resolution

Granted the Splunk user access using ACLs.

```bash
sudo setfacl -m u:splunk:rx /var/log/httpd
sudo setfacl -m u:splunk:r /var/log/httpd/access_log
sudo setfacl -m u:splunk:r /var/log/httpd/error_log
```

Restarted Splunk:

```bash
sudo /opt/splunk/bin/splunk restart
```

---

## Verification

Generated a new DVWA request.

Search:

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/brute"
```

New events immediately appeared in Splunk.

![alt text](../screenshots/Screenshot%202026-06-30%20115632.png)


---

## Lesson Learned

Always verify:

- Is the application generating logs?
- Is Splunk monitoring the correct file?
- Does Splunk have permission to read the log?

Most ingestion problems are caused by one of these three issues.

---

"Splunk stopped ingesting Apache logs. How would you troubleshoot?"

Now you have a real answer from your own lab.

Your troubleshooting process
✅ Verified Apache was still generating logs (tail -f).
✅ Confirmed Splunk had the correct monitor configuration (inputs.conf).
✅ Confirmed the logs were being written to the expected file.
✅ Checked directory and file permissions.
✅ Identified that Splunk lacked permission to read the log directory/files.
✅ Granted the necessary ACLs and verified ingestion resumed.


---