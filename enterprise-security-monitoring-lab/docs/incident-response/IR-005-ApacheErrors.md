
# Incident Report – IR-005

## Incident Information

| Field | Value |
|-------|-------|
| Incident ID | IR-005 |
| Incident Name | Apache Error Monitoring |
| Severity | Low |
| Detection Source | Splunk Enterprise |
| Date | 2026-07-06 |

---

# Executive Summary

Apache error logs were continuously monitored to identify application errors, configuration issues, and abnormal web server behavior.

During testing, several expected errors were observed while configuring DVWA and validating attack simulations.

---

# Detection Purpose

Apache error monitoring provides visibility into web server failures that may indicate:

- Application misconfiguration
- Exploitation attempts
- Missing files
- PHP errors
- Permission issues

---

# Timeline

| Time | Event |
|------|-------|
| Apache service started |
| Configuration changes applied |
| Error logs monitored |
| Events ingested into Splunk |

---

# Evidence

Observed Events

- Directory permission errors
- Apache startup messages
- PHP execution issues
- Server configuration warnings

---

# Analyst Investigation

Error logs were reviewed alongside Apache access logs to determine whether observed errors represented legitimate configuration issues or possible attack activity.

No evidence of successful server compromise was identified.

---

# Impact

Apache errors can reveal attempted exploitation, application failures, or operational issues requiring administrator attention.

---

# Recommendations

- Review error logs daily
- Monitor increases in HTTP 500 responses
- Investigate repeated application failures
- Enable centralized logging through Splunk

---

# Incident Status

Closed (Monitoring Validation)