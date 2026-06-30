
---

# Cross-Site Scripting (XSS) Detection

## MITRE ATT&CK

### Tactic

**Initial Access**

### Technique

**T1190 – Exploit Public-Facing Application**

---

## Attack Flow

        ```text
        Attacker (Kali)
                │
                ▼
        DVWA XSS Page
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

# What Happened?

This lab demonstrates both **Reflected XSS** and **Stored XSS** attacks against the DVWA web application.

Two different attack scenarios were performed to observe how each appears in Apache logs and Splunk.

---

# Part 1 – Reflected XSS

A normal request was submitted first:

```
hello!
```

Then the following XSS payload was entered:

```html
<script>alert(1)</script>
```

Apache recorded the request as:

```
GET /dvwa/vulnerabilities/xss_r/?name=%3Cscript%3Ealert%281%29%3C%2Fscript%3E
```

The payload was URL encoded before reaching the web server.

| Encoded | Decoded |
|----------|---------|
| `%3C` | `<` |
| `%3E` | `>` |
| `%28` | `(` |
| `%29` | `)` |
| `%2F` | `/` |

Decoded payload:

```html
<script>alert(1)</script>
```

The browser executed the JavaScript and displayed an alert box.

![alt text](<Screenshot 2026-06-30 130406.png>)

![alt text](<Screenshot 2026-06-30 130436.png>)
---

# Part 2 – Stored XSS

A guestbook entry was submitted using:

**Name**

```
Ayo
```

**Message**

```html
<script>alert(1)</script>
```

The payload was stored by the application.

Refreshing the page caused the JavaScript to execute again because it had been permanently saved in the guestbook.

![alt text](<Screenshot 2026-06-30 131439.png>)

![alt text](<Screenshot 2026-06-30 131635.png>)

Apache recorded multiple POST requests:

```
POST /dvwa/vulnerabilities/xss_s/
```

Unlike Reflected XSS, the malicious payload was **not visible** in the Apache access log.

![alt text](<Screenshot 2026-06-30 131737.png>)

---

## Splunk Search

### Reflected XSS

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/xss_r"
```

Result:

![alt text](<Screenshot 2026-06-30 130406-1.png>)

---

### Stored XSS

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/xss_s"
```

Result:

![alt text](<Screenshot 2026-06-30 131817.png>)

---

## Better Detection Queries

### Reflected XSS

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/xss_r"
| search "%3Cscript%3E" OR "alert(" OR "%3Cimg" OR "onerror"
| table _time host source sourcetype _raw
```

---

### Stored XSS

```spl
index=main sourcetype=apache_access "/dvwa/vulnerabilities/xss_s"
| table _time host source _raw
```

![alt text](<Screenshot 2026-06-30 132033.png>)

Since Apache access logs do not capture POST request bodies by default, the malicious JavaScript payload cannot be seen directly.

Detection relies on observing POST requests to the vulnerable endpoint.

---

## Reflected vs Stored XSS

| Feature | Reflected XSS | Stored XSS |
|----------|---------------|------------|
| Payload visible in Apache logs | ✅ Yes | ❌ No |
| Payload stored by application | ❌ No | ✅ Yes |
| Uses GET request | ✅ Yes | ❌ No |
| Uses POST request | ❌ No | ✅ Yes |
| Executes after page refresh | ❌ No | ✅ Yes |
| Easier to detect from Apache logs | ✅ Yes | ⚠️ More Difficult |

---

## Meaning

The Reflected XSS attack injected JavaScript through a URL parameter, making the payload visible in Apache logs.

The Stored XSS attack submitted the payload through a POST request and saved it in the application's database. Because Apache does not log POST bodies by default, only the request to the vulnerable endpoint was recorded.

This demonstrates why Stored XSS is often more difficult to detect using standard web server access logs alone.

---

## SOC Investigation

Questions an analyst would ask:

- Who submitted the malicious request?
- Was the payload successfully executed?
- Was the JavaScript stored by the application?
- Did other users access the infected page?
- Are additional XSS attempts present in the logs?
- Are web application logs or WAF logs available for further investigation?

---

## Incident Report Example

### Alert

**Possible Cross-Site Scripting (XSS) Attack**

### Severity

**High**

### Source IP

**192.168.52.10**

### Target

**DVWA XSS Modules**

- Reflected XSS
- Stored XSS

### Evidence

Reflected XSS contained the encoded JavaScript payload:

```html
<script>alert(1)</script>
```

Stored XSS generated repeated POST requests to:

```
/dvwa/vulnerabilities/xss_s/
```

followed by repeated execution of the stored JavaScript.

### MITRE

**T1190 – Exploit Public-Facing Application**

### Recommendation

If unauthorized:

- Block the attacking IP
- Validate and sanitize user input
- Encode output before rendering
- Implement Content Security Policy (CSP)
- Deploy a Web Application Firewall (WAF)
- Review application and database logs for stored payloads

---

## Lessons Learned

- Reflected XSS payloads are visible in Apache access logs because they are transmitted through URL parameters.
- Stored XSS payloads are submitted through POST requests and are not captured by default Apache access logging.
- Apache access logs alone may not be sufficient to investigate Stored XSS attacks.
- Additional visibility from application logs, reverse proxies, or WAFs can significantly improve detection.
- Understanding how different attack types are logged is just as important as detecting the attacks themselves.

---