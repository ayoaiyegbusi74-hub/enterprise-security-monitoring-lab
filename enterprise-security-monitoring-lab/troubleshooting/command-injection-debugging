
# Command Injection Troubleshooting

## Objective

Investigate why DVWA's default Command Injection module failed to display command output while other DVWA attack modules functioned correctly.

---

# Symptoms

Observed behavior:

- SQL Injection worked
- Reflected XSS worked
- Stored XSS worked
- Brute Force worked
- Apache logging worked

Only the Command Injection module failed.

The page simply refreshed without displaying any output.

---

# Initial Hypothesis

Possible causes considered:

- Apache permissions
- PHP disabled functions
- SELinux restrictions
- Missing ping binary
- Apache capability restrictions
- Splunk logging issues
- DVWA configuration

---

# Investigation

## Verify PHP Disabled Functions

Command:

```bash
php -i | grep disable_functions
```

Result:

```
disable_functions => no value
```

Conclusion:

PHP was not blocking `shell_exec()`.

---

## Verify Command Injection Source

Confirmed the vulnerable code contained:

```php
$cmd = shell_exec('ping -c 4 ' . $target);
```

---

## Verify Apache User

Created a temporary PHP test page.

Executed:

```
whoami
```

Result:

```
apache
```

---

Executed:

```
id
```

Result:

Apache service account information displayed successfully.

---

Executed:

```
hostname
```

Result:

Returned server hostname successfully.

---

Executed:

```
ls
```

Result:

Returned directory listing successfully.

---

Conclusion:

PHP successfully executed operating system commands.

---

## Ping Test

Executing:

```
ping
```

returned:

```
socktype: SOCK_RAW

Permission denied

missing cap_net_raw capability
```

---

## Verify Binary

```
ls -l /usr/bin/ping
```

Confirmed binary existed.

---

## Verify File Capabilities

Initially:

```
getcap /usr/bin/ping
```

returned nothing.

Capabilities were added:

```
sudo setcap cap_net_raw=ep /usr/bin/ping
```

Verification:

```
getcap /usr/bin/ping
```

returned:

```
cap_net_raw=ep
```

---

## Test as Apache

Executed:

```bash
sudo -u apache bash
```

Then:

```bash
/usr/bin/ping -c 2 127.0.0.1
```

Result:

Ping executed successfully.

Conclusion:

Apache user was capable of running ping directly.

---

## Remaining Issue

Despite:

- PHP executing commands
- Apache user executing ping
- File capabilities configured

the DVWA Command Injection page continued to display no output using the original implementation.

---

# Workaround

For demonstration purposes only:

Changed:

```php
$cmd = shell_exec('ping -c 4 ' . $target);
```

to

```php
$cmd = shell_exec($target . ' 2>&1');
```

Testing with:

```
whoami
```

successfully returned:

```
apache
```

confirming Command Injection functionality.

---

# Root Cause

The precise root cause could not be fully identified during the lab.

Evidence suggests the issue is related to the interaction between:

- PHP
- Apache
- RHEL 9
- `shell_exec()`
- `ping`
- Linux capabilities

rather than the Command Injection vulnerability itself.

---

# Lessons Learned

This troubleshooting exercise demonstrated:

- Apache log analysis
- Linux capability troubleshooting
- PHP command execution validation
- SELinux verification
- Apache user testing
- Structured root cause analysis

---

# Final Outcome

Although the default `ping` implementation remained unresolved, the Command Injection vulnerability itself was successfully demonstrated using alternate operating system commands.

The lab objective of demonstrating arbitrary command execution was successfully achieved.