# Enterprise Security Monitoring & Vulnerability Lab

## Overview
This project simulates an enterprise Security Operations Center (SOC) environment using virtual machines, SIEM tools (Splunk), and offensive security tools to build hands-on cybersecurity experience.

## Architecture
- Kali Linux (Attacker)
- RHEL Linux (SIEM / Splunk Server)
- Windows VM (Endpoint simulation)

## Tools Used
- Splunk Enterprise
- Wireshark
- Nmap
- Nessus
- Hydra
- DVWA
- Bash / PowerShell

## Objectives
- Centralized log monitoring
- Attack simulation & detection
- Incident response practice
- Vulnerability scanning
- Network traffic analysis

## Key Skills Demonstrated
- SIEM monitoring & alerting
- Log analysis
- Threat detection
- Network troubleshooting
- Security investigation workflows

## Lab Diagram

            ┌──────────────────────┐
            │   Kali Linux VM      │
            │   (Attacker)         │
            └─────────┬────────────┘
                      │
                      │ Host-Only Network
                      │ (19.168.52.0/24)
                      │
            ┌─────────▼────────────┐
            │    RHEL VM           │
            │ (Target + DVWA)      │
            │ + Splunk Forwarder   │
            └─────────┬────────────┘
                      │
              NAT Internet Access
                      │
            ┌─────────▼────────────┐
            │ Splunk Enterprise    │
            │ (SIEM Dashboard)     │
            └──────────────────────┘

## Project Phases
- Phase 1: Lab Setup
- Phase 2: SIEM Configuration
- Phase 3: Attack Simulation
- Phase 4: Incident Response