```markdown
# Network Architecture

## Overview

This lab uses a dual-network VMware architecture:

- **NAT Network**: Provides internet access for system updates and package installations.
- **VMnet2 Host-only Network**: Isolated security lab network used for attacker-to-target communication, monitoring, and attack simulations.

---

## Network Diagram

```

```
                     Internet
                        |
                      NAT Network

          --------------------------------
          |                              |
       Kali Linux                    RHEL Server
       Attacker                     Target / SIEM

    eth0 (NAT)                   ens160 (NAT)
 192.168.240.128              192.168.41.129


          VMnet2 Host-only Lab Network

                192.168.52.0/24


    eth1 (Lab)                  ens224 (Lab)

  192.168.52.10              192.168.52.20


        Kali  ----------------->  RHEL
         Attacker                  Target/SIEM
```

````

---

# Lab Network Details

## Subnet

```text
192.168.52.0/24
````

Network Information:

| Item              | Value                         |
| ----------------- | ----------------------------- |
| Network Address   | 192.168.52.0                  |
| Subnet Mask       | 255.255.255.0                 |
| Usable IP Range   | 192.168.52.1 - 192.168.52.254 |
| Broadcast Address | 192.168.52.255                |

---

# RHEL Server (Target / SIEM)

## Network Interface

```text
Interface: ens224
IP Address: 192.168.52.20/24
```

## Role

* Target machine
* Splunk SIEM server
* DVWA vulnerable web application
* Apache web server
* Security log collector

---

# Kali Linux (Attacker)

## Network Interface

```text
Interface: eth1
IP Address: 192.168.52.10/24
```

## Role

* Attacker machine
* Network reconnaissance
* Vulnerability testing
* Nmap scanning
* Hydra brute-force simulations
* Web attack simulations

---

# Network Communication Flow

```
Kali Linux
(192.168.52.10)
        |
        |
   VMnet2 Host-only
        |
        |
RHEL Server
(192.168.52.20)

```

Kali performs security testing against RHEL, while RHEL collects and analyzes activity through the SIEM stack.

```


