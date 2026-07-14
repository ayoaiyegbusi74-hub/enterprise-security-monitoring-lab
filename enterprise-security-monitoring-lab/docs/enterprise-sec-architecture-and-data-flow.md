
# Enterprise Security Architecture & Data Flow

## Purpose

This project demonstrates the enterprise security lifecycle of a web application, from vulnerability identification and attack simulation to threat detection, alerting, and incident response. While the lab primarily focused on penetration testing and security monitoring, the architecture also illustrates where vulnerability management and endpoint detection fit within a real-world enterprise environment.

---

# Enterprise Security Workflow

```text
                    Security Team
                          │
                          ▼
          Vulnerability Scan (Nessus/Qualys)
                          │
          Identify Known Vulnerabilities
                          │
      Prioritize Risks & Recommend Remediation
                          │
                          ▼
           Penetration Testing (Kali Linux)
                          │
        Validate Whether Vulnerabilities
             Can Actually Be Exploited
                          │
                          ▼
            RHEL Web Server Hosting DVWA
                          │
          ┌───────────────┼────────────────┐
          ▼               ▼                ▼
 Apache Access Logs   Linux System Logs   EDR (CrowdStrike)
          │               │                │
          └───────────────┼────────────────┘
                          ▼
                 Splunk Enterprise (SIEM)
                          │
          Correlation • Detection • Alerting
                          │
                          ▼
               SOC Investigation & Response
```

---

# Vulnerability Management

## Purpose

Vulnerability scanning is a proactive security practice used to identify known weaknesses before attackers can exploit them. Rather than attacking a system, the scanner evaluates software versions, operating system configurations, running services, and known Common Vulnerabilities and Exposures (CVEs).

**Objective**

Identify security weaknesses early so they can be remediated before they become security incidents.

---

## How Vulnerability Scanning Fits Into This Project

Although a vulnerability scanner was not deployed in this lab, an enterprise environment would typically perform vulnerability scans against the RHEL web server before penetration testing.

A tool such as Nessus would evaluate:

* Operating system patch level
* Apache HTTP Server version
* OpenSSL version
* Exposed network services
* SSL/TLS configuration
* Weak or insecure configurations
* Missing security updates
* Known CVEs affecting installed software

The scan would generate a prioritized report that allows administrators to remediate vulnerabilities before attackers attempt exploitation.

---

## Typical Vulnerability Scanning Workflow

1. Discover live hosts on the network.
2. Identify open ports and running services.
3. Determine software and operating system versions.
4. Compare discovered software against known CVEs.
5. Identify insecure configurations and missing patches.
6. Assign severity ratings (Critical, High, Medium, Low).
7. Generate remediation recommendations.
8. Re-scan after remediation to verify that vulnerabilities have been resolved.

---

## Example Findings

A vulnerability scanner might report findings such as:

| Severity | Example Finding                        | Recommendation                                      |
| -------- | -------------------------------------- | --------------------------------------------------- |
| Critical | Apache version affected by a known CVE | Upgrade Apache to the latest supported version      |
| High     | Weak SSH encryption algorithms enabled | Disable legacy ciphers and enable strong encryption |
| Medium   | Missing HTTP security headers          | Configure recommended security headers              |
| Low      | Self-signed SSL certificate            | Replace with a trusted certificate if appropriate   |

---

## Vulnerability Scanning vs Penetration Testing

Although they are closely related, they serve different purposes.

**Vulnerability Scanning**

* Automated
* Identifies known weaknesses
* Does not attempt exploitation
* Produces remediation recommendations

**Penetration Testing**

* Simulates a real attacker
* Attempts to exploit vulnerabilities
* Validates whether weaknesses are actually exploitable
* Demonstrates business impact

A simple way to remember the difference is:

> **Vulnerability scanners identify weaknesses. Penetration testers determine whether those weaknesses can actually be exploited.**

---

## Interview Talking Point

> "Although this project focused on penetration testing and SIEM-based detection, an enterprise deployment would begin with vulnerability management. A tool such as Nessus would scan the RHEL server for known vulnerabilities, insecure configurations, and missing patches. Those findings would be prioritized for remediation or validated through controlled penetration testing using Kali Linux. The attack activity would then generate logs analyzed by Splunk, while an EDR such as CrowdStrike would monitor endpoint behavior during and after exploitation."

---

## Enterprise Security Lifecycle

1. Assets are deployed.
2. Vulnerability scanners identify known security weaknesses.
3. Administrators remediate discovered vulnerabilities.
4. Penetration testers validate whether remaining weaknesses are exploitable.
5. Servers generate application and operating system logs.
6. EDR monitors endpoint activity for malicious behavior.
7. Splunk correlates logs from multiple sources and generates alerts.
8. SOC analysts investigate alerts and initiate incident response.
9. Security teams remediate findings and continuously improve detections.


# Future Enhancements

Although this project successfully demonstrates enterprise security monitoring, detection engineering, alerting, and incident response using Splunk Enterprise, several enhancements could further align the environment with a production enterprise security architecture.

---

## 1. Vulnerability Management Integration

**Objective**

Integrate automated vulnerability scanning into the lab to identify security weaknesses before simulated attacks occur.

**Planned Implementation**

* Deploy Nessus Essentials to perform authenticated and unauthenticated vulnerability scans.
* Scan the RHEL web server for:

  * Missing security patches
  * Known CVEs
  * Insecure configurations
  * Weak SSL/TLS settings
  * Exposed network services
* Generate vulnerability assessment reports.
* Validate remediation by performing follow-up scans after vulnerabilities have been addressed.

**Enterprise Value**

Introduces a proactive security layer by identifying weaknesses before they are exploited.

---

## 2. Endpoint Detection & Response (EDR)

**Objective**

Expand endpoint visibility by deploying an enterprise EDR platform such as CrowdStrike Falcon.

**Planned Implementation**

Install an EDR agent on the RHEL server to monitor:

* Process execution
* Command-line activity
* Parent-child process relationships
* File creation and modification
* Privilege escalation
* Persistence mechanisms
* Suspicious endpoint behavior

Forward EDR detections to Splunk to provide centralized visibility alongside Apache and Linux system logs.

**Enterprise Value**

Provides deep endpoint telemetry that complements SIEM log analysis and enables faster detection of post-exploitation activity.

---

## 3. SIEM Correlation with Vulnerability Data

**Objective**

Correlate vulnerability assessment results with security events to improve investigation and prioritization.

**Planned Implementation**

* Import Nessus scan results into Splunk.
* Correlate active vulnerabilities with attack activity.
* Prioritize alerts affecting systems with Critical or High severity vulnerabilities.
* Create dashboards showing vulnerability status alongside security detections.

**Enterprise Value**

Allows analysts to prioritize incidents based on both exploit attempts and the actual exposure of affected systems.

---

## 4. Expanded Attack Simulations

Increase attack coverage by simulating additional adversary techniques, including:

* Cross-Site Scripting (XSS)
* Cross-Site Request Forgery (CSRF)
* Directory Traversal
* Local File Inclusion (LFI)
* Remote File Inclusion (RFI)
* Remote Code Execution (RCE)
* Web shell deployment
* Reverse shell establishment

Each attack would include corresponding Splunk detections, alerts, dashboards, and incident response documentation.

---

## 5. Threat Intelligence Integration

Enhance Splunk by integrating external threat intelligence feeds.

Potential capabilities include:

* IOC matching
* Malicious IP reputation
* Domain reputation
* Hash lookups
* Automated enrichment during investigations

**Enterprise Value**

Improves detection accuracy and accelerates SOC investigations.

---

## 6. Automated Response

Introduce Security Orchestration, Automation, and Response (SOAR) capabilities.

Potential automated actions include:

* Blocking malicious IP addresses
* Isolating compromised hosts
* Creating incident tickets
* Notifying security teams
* Launching investigation playbooks

**Enterprise Value**

Reduces analyst response time and improves incident handling consistency.

---

## 7. Cloud Security Monitoring

Extend the monitoring environment to include cloud infrastructure.

Potential additions include:

* AWS CloudTrail
* AWS GuardDuty
* Microsoft Azure Activity Logs
* Microsoft Defender for Cloud
* Google Cloud Audit Logs

This would demonstrate hybrid on-premises and cloud security monitoring.

---

## 8. Enterprise Authentication Monitoring

Expand monitoring beyond Apache by integrating authentication services such as:

* Active Directory
* LDAP
* SSH authentication logs
* Sudo activity
* Privilege escalation events

This would enable identity-focused detections and investigations.

---

## 9. Security Metrics & Executive Dashboards

Develop executive-level dashboards displaying:

* Total attacks detected
* Detection trends
* Incident severity distribution
* Mean Time to Detect (MTTD)
* Mean Time to Respond (MTTR)
* Vulnerability remediation progress
* Top attacking IP addresses
* Most targeted applications

These dashboards would support both SOC analysts and security leadership.

---

# Project Roadmap

| Phase                                |    Status   |
| ------------------------------------ | :---------: |
| Environment Deployment               | ✅ Completed |
| Apache & DVWA Configuration          | ✅ Completed |
| Splunk Deployment                    | ✅ Completed |
| Log Ingestion                        | ✅ Completed |
| Detection Engineering                | ✅ Completed |
| Alerts                               | ✅ Completed |
| Dashboards                           | ✅ Completed |
| Incident Response Documentation      | ✅ Completed |
| GitHub Documentation                 | ✅ Completed |
| Enterprise Security Architecture     | ✅ Completed |
| Vulnerability Management Integration |  🔄 Planned |
| EDR Integration                      |  🔄 Planned |
| Threat Intelligence Integration      |  🔄 Planned |
| SOAR Automation                      |  🔄 Planned |
| Cloud Security Monitoring            |  🔄 Planned |

---

# Final Project Summary

This project demonstrates the complete workflow of an enterprise Security Operations Center (SOC), beginning with attack simulation and progressing through centralized log collection, detection engineering, alerting, dashboard development, and incident response. The planned enhancements outline a clear roadmap for evolving the lab into a more comprehensive enterprise security platform by incorporating vulnerability management, endpoint detection and response, threat intelligence, automation, and cloud security monitoring. This phased approach reflects how security capabilities are typically implemented and matured within modern enterprise environments.
