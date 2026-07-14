
---

# 1. Tell me about your security monitoring project.

> I built an enterprise security monitoring lab using VMware, RHEL, Kali Linux, Apache, DVWA, and Splunk Enterprise. Kali Linux simulated attacks such as brute-force, SQL injection, command injection, and malicious file uploads against the DVWA application hosted on the RHEL server. Apache generated access and error logs, which were ingested into Splunk. I then created field extractions, detection rules, alerts, dashboards, and incident reports to simulate how a SOC analyst monitors, detects, and investigates security events in an enterprise environment.

---

# 2. Why did you choose RHEL instead of Ubuntu?

> I chose RHEL because it's widely used in enterprise environments, especially in government, healthcare, and large organizations. I wanted hands-on experience with an operating system that I'd likely encounter in production. It also gave me experience working with SELinux, firewalld, and enterprise Linux administration.

---

# 3. Why did you choose Splunk instead of CrowdStrike?

> Splunk and CrowdStrike serve different purposes. Splunk is a SIEM that collects and analyzes logs from multiple sources to detect and investigate security events. CrowdStrike is an EDR that monitors activity directly on an endpoint, such as process execution and suspicious behavior. Since my goal was to build a security monitoring and detection lab, Splunk was the better fit. In a production environment, I would use both together.

---

# 4. Explain how Apache logs were ingested into Splunk.

> Apache automatically generated access and error logs as users interacted with the web application. I configured Splunk to monitor those log files using `inputs.conf`. Splunk continuously read new log entries, indexed them, and made them searchable. I then created field extractions so the data could be used for searches, dashboards, and detection rules.

---

# 5. Walk me through one of your detection rules.

> One detection rule monitored for repeated failed login attempts, which could indicate a brute-force attack. The search counted failed authentication attempts from the same IP address over a period of time. If the number of failures exceeded the threshold, Splunk generated an alert. The alert included information such as the source IP, timestamp, and number of failed attempts, allowing an analyst to investigate further.

---

# 6. What's the difference between a detection, an alert, a threat hunt, and a correlation search?

> A **detection** is a rule that identifies suspicious activity. An **alert** is the notification generated when a detection rule is triggered. A **threat hunt** is a proactive investigation where analysts search for threats that haven't generated alerts. A **correlation search** combines data from multiple sources to identify patterns that may indicate an attack, improving detection accuracy.

---

# 7. Explain one incident investigation from start to finish.

> During a simulated brute-force attack, Splunk generated an alert after detecting multiple failed login attempts from the same IP address. I reviewed the Apache logs to verify the activity, identified the attacking IP address, and checked whether any login attempts were successful. I documented my findings, mapped the activity to MITRE ATT&CK, assessed the impact, and recommended blocking the source IP and strengthening authentication controls.

---

# 8. How did you map your findings to MITRE ATT&CK?

> After creating each detection, I identified the corresponding MITRE ATT&CK tactic and technique. For example, the brute-force detection was mapped to Credential Access using technique **T1110 (Brute Force)**, while SQL injection was mapped to **T1190 (Exploit Public-Facing Application)**. Mapping detections to MITRE provided a standardized way to classify attacker behavior and communicate findings.

---

# 9. If this lab were deployed in production, what would you improve?

> I would add vulnerability management using Nessus, deploy an EDR solution such as CrowdStrike Falcon, integrate firewall and Active Directory logs, implement threat intelligence feeds, and automate parts of the incident response process using SOAR. These improvements would provide broader visibility, stronger detection capabilities, and faster response times.

---

# 10. Why did you use DVWA?

> DVWA is a deliberately vulnerable web application designed for security training. It allowed me to safely simulate real-world web attacks such as SQL injection, command injection, and file upload attacks, while generating realistic logs for monitoring and detection in Splunk.

---

# 11. What was the biggest challenge you faced during the project?

> One of the biggest challenges was troubleshooting Apache log ingestion into Splunk. Although Apache was generating logs correctly, Splunk wasn't indexing new events initially. I worked through the issue by verifying Apache logging, checking Splunk's `inputs.conf`, validating file permissions, and confirming field extractions. Solving that problem gave me a better understanding of Linux permissions, log ingestion, and troubleshooting SIEM data pipelines.

---

# 12. Why would an organization run a Nessus scan before a penetration test?

> Organizations perform a Nessus vulnerability scan first to identify known vulnerabilities, missing patches, and insecure configurations. The scan helps prioritize risks and provides a roadmap for penetration testers. Instead of blindly attacking a system, the penetration tester can focus on validating whether the identified vulnerabilities can actually be exploited.

---

# 13. Why can't Splunk alone replace CrowdStrike?

> Splunk and CrowdStrike have different roles. Splunk is a SIEM that collects and analyzes logs from multiple systems to detect and investigate security events. CrowdStrike is an EDR that runs on the endpoint and monitors operating system activity, such as process execution, file changes, and suspicious behavior. Splunk provides centralized visibility, while CrowdStrike provides deep endpoint visibility. In a production environment, they work together rather than replacing each other.

---

# 14. During your project, what role was Kali Linux playing?

> Kali Linux acted as the attack machine in my lab. It was used to simulate real-world attacks against the DVWA application on the RHEL server, including brute-force attacks, SQL injection, command injection, and malicious file uploads. These attacks generated logs that were ingested into Splunk, allowing me to develop detections, alerts, dashboards, and incident investigations.

---

# 15. What's the difference between vulnerability scanning and penetration testing?

> Vulnerability scanning is an automated process that identifies known security weaknesses without exploiting them. Penetration testing goes a step further by attempting to exploit those weaknesses to determine whether they are actually exploitable and what impact they could have. In simple terms, vulnerability scanning finds potential problems, while penetration testing validates them.

---

## Easy to Remember

> **Nessus identifies the weakness, Kali attempts to exploit it, Apache and Linux generate the logs, CrowdStrike monitors endpoint behavior, and Splunk correlates the data to detect and investigate the attack.**

