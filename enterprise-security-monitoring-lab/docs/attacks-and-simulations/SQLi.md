
---

# SQL Injection Detection

## MITRE ATT&CK

### Tactic

**Initial Access**

### Technique

**T1190 – Exploit Public-Facing Application**

---

## Attack Flow

        ```
        Attacker (Kali)
                │
                │
                ▼
        DVWA SQL Injection Page
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

## What Happened?

From Kali, I tested the DVWA SQL Injection page by entering a normal value first:

```
1
```

Then I submitted the SQL Injection payload:

```sql
1' OR '1'='1
```

Target page:

```
http://192.168.52.20/dvwa/vulnerabilities/sqli/
```

Apache recorded the request as:

```
GET /dvwa/vulnerabilities/sqli/?id=1%27+OR+%271%27%3D%271&Submit=Submit HTTP/1.1
```

The payload was URL encoded before being sent to the web server.

| Encoded | Decoded |
|----------|---------|
| `%27` | `'` |
| `+` | Space |
| `%3D` | `=` |

Resulting payload:

```sql
1' OR '1'='1
```

![alt text](../screenshots/Screenshot%202026-06-30%20124008.png)

---

## Splunk Search

Run:

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/sqli"
```

Result:

![alt text](../screenshots/Screenshot%202026-06-30%20123651.png)

The search returns all requests made to the SQL Injection page.

---

## Better Detection Query

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/sqli"
| search "%27" OR "%3D" OR "UNION" OR "SELECT" OR "OR" OR "1%"
| table _time host source sourcetype _raw
```

Result:

![alt text](../screenshots/Screenshot%202026-06-30%20123902.png)

---

## Meaning

The request contains SQL syntax inside a user-supplied parameter.

Instead of sending a normal numeric value (`id=1`), the user attempted to manipulate the SQL query by injecting:

```sql
' OR '1'='1
```

This is a classic SQL Injection payload commonly used to bypass authentication or retrieve unauthorized data.

---

## SOC Investigation

Questions an analyst would ask:

- **Who initiated the request?**
- **Was the application vulnerable?**
- **Did the payload successfully return unauthorized data?**
- **Were additional SQL Injection attempts observed?**
- **Did the attacker continue with further exploitation?**

---

## Incident Report Example

### Alert

**Possible SQL Injection Attempt**

### Severity

**High**

### Source IP

**192.168.52.10**

### Target

**DVWA SQL Injection Module**

### Evidence

The request contained SQL syntax within the URL parameter:

```sql
1' OR '1'='1
```

Apache logged the encoded payload:

```
id=1%27+OR+%271%27%3D%271
```

### MITRE

**T1190 – Exploit Public-Facing Application**

### Recommendation

If unauthorized:

- Block the source IP
- Review web server logs for additional payloads
- Inspect database logs for successful exploitation
- Validate input on all web forms
- Implement parameterized queries (prepared statements)
- Deploy a Web Application Firewall (WAF)

---