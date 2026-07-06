
---

# Brute Force Attack Detection

## MITRE ATT&CK

### Tactic
**Credential Access**

### Technique
**T1110.001 – Password Guessing**

---

## Attack Flow

        ```text
        Attacker (Kali)
                │
                │
                ▼
        DVWA Brute Force Page
                │
                ▼
        Apache Access Log
                │
                ▼
        Splunk Enterprise
                │
                ▼
        SOC Investigation
        ```
---

## What Happened?

From Kali, I attempted multiple logins against the DVWA Brute Force page using different passwords for the **admin** account.

Target page:

```
http://192.168.52.20/dvwa/vulnerabilities/brute/
```

requests observed in Apache logs:

```
GET /dvwa/vulnerabilities/brute/?username=admin&password=1234567789&Login=Login HTTP/1.1

GET /dvwa/vulnerabilities/brute/?username=admin&password=password&Login=Login HTTP/1.1
```

![alt text](../screenshots/Screenshot%202026-06-30%20115436.png)

![alt text](../screenshots/Screenshot%202026-06-30%20115500.png)
---

## Splunk Search

Run:

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/brute"
```

Result:

![alt text](../screenshots/Screenshot%202026-06-30%20115632.png)

The search returns all requests made to the DVWA Brute Force page.

---

## Better Detection Query

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/brute"
| stats count by clientip
```

### Result

| Client IP | Count |
|-----------|-------|
| **192.168.52.10** | Multiple Attempts |

### Meaning

Host **192.168.52.10** generated repeated login attempts against the DVWA Brute Force page, indicating possible password guessing activity.

---

## SOC Investigation

Questions an analyst would ask:

- **Who owns 192.168.52.10?**
- **Is this penetration testing or an actual attack?**
- **Did any login attempt succeed?**
- **Were other web applications targeted?**
- **Should this IP be blocked or monitored?**

---

## Incident Report

### Alert

**Possible Web Application Brute Force Attack**

### Severity

**Medium**

### Source IP

**192.168.52.10**

### Target

**DVWA Brute Force Module**

### Evidence

Multiple requests were made to:

```
/dvwa/vulnerabilities/brute/
```

using different password values for the **admin** account.

### MITRE

**T1110.001 – Password Guessing**

### Recommendation

If unauthorized:

- **Block the source IP**
- **Review authentication logs**
- **Check for successful logins**
- **Enable account lockout and MFA**
- **Continue monitoring for additional attacks**

---







