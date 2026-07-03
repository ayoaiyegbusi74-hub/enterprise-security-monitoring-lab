
# Kali Linux Installation

## Project

Enterprise Security Monitoring & Vulnerability Lab

---

# Objective

Deploy Kali Linux as the attacker workstation used to simulate web attacks against DVWA.

---

# VM Specifications

| Setting | Value |
|---------|------|
| Operating System | Kali Linux |
| Hypervisor | VMware Workstation Pro |
| vCPUs | 2 |
| RAM | 4 GB |
| Disk | 40 GB |
| Network Adapter 1 | NAT |
| Network Adapter 2 | Host-Only |

---

# Installation

Installed using the official Kali Linux ISO.

Configured:

- Standard desktop
- SSH client
- Firefox
- Default penetration testing tools

---

# Network

## NAT

Internet connectivity.

Obtained automatically.

---

## Host-Only

Static address.

```
192.168.52.10
```

---

# Connectivity Testing

Ping RHEL.

```
ping 192.168.52.20
```

Browse DVWA.

```
http://192.168.52.20/dvwa
```

SSH.

```
ssh ayo@192.168.52.20
```

---

# Tools Used

- Firefox
- curl
- Hydra
- Nmap
- Netcat
- Bash

---

# Role

The Kali VM simulated:

- SQL Injection
- Stored XSS
- Brute Force
- Command Injection
- File Upload

against the RHEL-hosted DVWA application.

---

# Lessons Learned

Separating the attacker and target systems on a Host-Only network closely resembles a real internal enterprise environment while allowing traffic collection through Apache and Splunk.