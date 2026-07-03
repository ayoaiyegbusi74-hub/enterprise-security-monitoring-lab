
# RHEL 9 Installation

## Project

Enterprise Security Monitoring & Vulnerability Lab

---

# Objective

Deploy a Red Hat Enterprise Linux 9 virtual machine that serves as the target system, web server, and SIEM host for the lab.

---

# VM Specifications

| Setting | Value |
|---------|------|
| Operating System | RHEL 9 |
| Hypervisor | VMware Workstation Pro |
| vCPUs | 4 |
| Memory | 8 GB |
| Disk | 80 GB |
| Firmware | UEFI |
| Network Adapter 1 | NAT |
| Network Adapter 2 | Host-Only |

---

# Installation Steps

1. Download the RHEL 9 ISO from the Red Hat Developer Portal.
2. Create a new virtual machine in VMware Workstation Pro.
3. Assign hardware resources.
4. Boot from the ISO.
5. Complete the graphical installer.
6. Create a local administrator account.
7. Enable networking.
8. Reboot.

---

# Network Configuration

## NAT Adapter

Purpose:

- Internet access
- Software installation
- Updates

Obtained via DHCP.

Example:

```
192.168.41.129
```

---

## Host-Only Adapter

Purpose:

Dedicated lab network between Kali and RHEL.

Configured manually.

Address:

```
192.168.52.20/24
```

---

# Installed Packages

Apache

```
sudo dnf install httpd -y
```

PHP

```
sudo dnf install php php-mysqlnd -y
```

MariaDB

```
sudo dnf install mariadb-server -y
```

Git

```
sudo dnf install git -y
```

---

# Services

Enabled:

```
sudo systemctl enable --now httpd
sudo systemctl enable --now mariadb
```

---

# Firewall

Allowed services:

```
http
https
ssh
```

Commands:

```
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

---

# SELinux

SELinux remained in **Enforcing** mode throughout the project.

Several issues were resolved using proper SELinux contexts rather than disabling SELinux.

---

# Hostname

```
rhel-siem
```

---

# Verification

```
hostnamectl
ip addr
systemctl status httpd
systemctl status mariadb
getenforce
```

Expected:

- Apache running
- MariaDB running
- SELinux Enforcing
- Hostname configured
- Network connectivity established

---

# Lessons Learned

Maintaining SELinux in Enforcing mode provided valuable experience troubleshooting enterprise Linux environments instead of bypassing security controls.