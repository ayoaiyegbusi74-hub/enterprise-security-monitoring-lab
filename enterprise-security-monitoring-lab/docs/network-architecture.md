# Network Architecture

                 Internet
                    |
                  NAT

        -------------------------
        |                       |
     Kali                    RHEL
   eth0/NAT                ens160/NAT
192.168.240.128        192.168.41.129


        VMnet2 Host-only Lab Network

        192.168.52.0/24


     Kali eth1              RHEL ens224

   192.168.52.10          192.168.52.20

        Attacker  --->  Target/SIEM

### Subnet

```text
192.168.52.0/24
```

* Network address: `192.168.52.0`
* Usable IP range: `192.168.52.1 - 192.168.52.254`
* Broadcast: `192.168.52.255`
* Subnet mask: `255.255.255.0`

---

### RHEL (Target / SIEM Server)

**Lab interface/IP:**

ens224
192.168.52.20/24

Role:
    Target machine
    Splunk SIEM
    DVWA
    Web server
    Log collector

---

### Kali Linux (Attacker)

**Lab interface/IP:**

eth1
192.168.52.10/24

Role:
    Attacker machine
    Nmap
    Hydra
    Web attacks
    Security testing

---


