
# VMware Network Configuration Troubleshooting

## Overview

This document describes the troubleshooting process used to resolve network connectivity issues encountered during the initial deployment of the Enterprise Security Monitoring Lab.

The lab consisted of two virtual machines hosted in VMware Workstation Pro:

- Red Hat Enterprise Linux 9 (Apache + DVWA + Splunk Enterprise)
- Kali Linux (Attacker)

The objective was to establish reliable communication between both virtual machines while maintaining Internet connectivity for software installation and updates.

---

# Environment

| Component | Value |
|----------|-------|
| Hypervisor | VMware Workstation Pro |
| Host Operating System | Windows 11 |
| Target VM | Red Hat Enterprise Linux 9 |
| Attacker VM | Kali Linux |
| Network Configuration | NAT + Host-Only |

---

# Problem Statement

The Kali Linux and RHEL virtual machines were unable to communicate over the intended lab network.

Observed issues included:

- Ping requests failed.
- Apache web server was unreachable.
- Attack simulations could not reach the DVWA application.
- Splunk detections could not be validated because no attack traffic reached the target server.

---

# Symptoms

The following symptoms were observed during testing:

- `ping 192.168.52.20` failed from Kali.
- `ping 192.168.52.10` failed from RHEL.
- `curl http://192.168.52.20` returned connection errors.
- VMware adapters showed inconsistent IP addressing.
- Incorrect Host-Only subnet configuration.

---

# Root Cause Analysis

The connectivity issue resulted from multiple network configuration problems:

- Incorrect Host-Only network configuration.
- Network adapters assigned to the wrong VMware network.
- Incorrect static IP assignments.
- Duplicate or incorrect interface addressing.
- Routing inconsistencies between NAT and Host-Only interfaces.

---

# Investigation Process

The following steps were performed to isolate the issue.

## Step 1 — Verify Interface Configuration

Checked available interfaces.

```bash
ip addr
```

Verified:

- ens160
- ens224

---

## Step 2 — Verify Routing

```bash
ip route
```

Confirmed:

- NAT gateway
- Host-Only subnet

---

## Step 3 — Verify VMware Configuration

Validated:

- NAT Adapter
- Host-Only Adapter

Confirmed both virtual machines were connected to:

- NAT
- VMnet2 Host-Only

---

## Step 4 — Verify Static IP Addresses

Configured Host-Only addresses.

### Kali

```
192.168.52.10/24
```

### RHEL

```
192.168.52.20/24
```

---

## Step 5 — Verify Connectivity

Executed:

```bash
ping 192.168.52.20
```

and

```bash
ping 192.168.52.10
```

Connectivity was successfully restored.

---

# Resolution

The following corrections resolved the issue.

- Configured VMnet2 as the dedicated Host-Only network.
- Assigned static IP addresses to both virtual machines.
- Verified subnet mask consistency.
- Corrected interface assignments.
- Confirmed routing tables.
- Restarted network interfaces.

---

# Validation

Connectivity was verified using:

```bash
ping
```

```bash
curl
```

```bash
ip addr
```

```bash
ip route
```

Apache became reachable from Kali and attack simulations were successfully executed against DVWA.

---

# Impact

Resolving the networking issue enabled:

- Successful communication between attacker and target.
- Apache accessibility.
- DVWA attack simulations.
- Splunk log generation.
- Detection engineering validation.
- Threat hunting exercises.

Without resolving this issue, the remainder of the project could not have proceeded.

---

# Lessons Learned

Virtualized lab environments require careful planning of network topology.

Using both NAT and Host-Only adapters provides:

- Internet access for software installation.
- Isolated communication for attack simulations.

Proper IP addressing and interface validation should always be performed before troubleshooting higher-layer services.

---

# Best Practices

- Use dedicated Host-Only networks for security labs.
- Assign static IP addresses to lab systems.
- Verify interface configuration before application troubleshooting.
- Validate routing tables after network changes.
- Test connectivity using ping and curl before investigating application-level issues.