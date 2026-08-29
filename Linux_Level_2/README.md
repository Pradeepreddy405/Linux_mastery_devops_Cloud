# 🐧 Level 2 — Linux Administration & Troubleshooting

This level builds on Linux fundamentals and focuses on **system administration, networking, services, security, troubleshooting, automation, and production-oriented Linux tasks**.

---

## 📚 Learning Roadmap

```text
LEVEL 2 — LINUX ADMINISTRATION & TROUBLESHOOTING
│
├── 01. Scheduling & Automation
│   └── Create a Cron Job
│
├── 02. Linux User & Access Management
│   ├── Linux Collaborative Directories
│   ├── Linux Configure sudo
│   └── Linux SSH Authentication
│
├── 03. Linux System Configuration
│   ├── Linux Banner
│   ├── Install a Package
│   ├── Configure Local Yum Repositories
│   └── Linux Services
│
├── 04. File Search & Text Processing
│   ├── Linux Find Command
│   └── Linux String Substitute (sed)
│
├── 05. Linux Networking & Firewall
│   ├── DNS Troubleshooting
│   └── Linux Firewalld Setup
│
├── 06. Linux Mail Services
│   ├── Linux Postfix Mail Server
│   └── Linux Postfix Troubleshooting
│
├── 07. Load Balancing
│   ├── Install and Configure HAProxy LBR
│   └── HAProxy LBR Troubleshooting
│
├── 08. Database Troubleshooting
│   └── MariaDB Troubleshooting
│
├── 09. Bash Automation
│   └── Linux Bash Scripts
│
├── 10. Web Server Administration
│   ├── Add Response Headers in Apache
│   └── Apache Troubleshooting
│
├── 11. Security
│   ├── Linux GPG Encryption
│   └── Application Security
│
├── 12. System Maintenance
│   └── Linux LogRotate
│
└── 13. Configuration Management
    └── Install Ansible
```

---

# 01. Scheduling & Automation

## Create a Cron Job

Cron is used to execute commands and scripts automatically at scheduled times.

### Key Concepts

- `cron`
- `crontab`
- Cron schedules
- `/etc/crontab`
- User cron jobs
- Cron permissions
- Automated tasks

Example:

```bash
crontab -e
```

---

# 02. Linux User & Access Management

## Linux Collaborative Directories

Collaborative directories allow multiple users to work with shared files while maintaining appropriate permissions.

### Key Concepts

- Groups
- Shared directories
- `setgid`
- `chmod`
- `chown`
- Group ownership
- Access control

---

## Linux Configure sudo

`sudo` provides controlled administrative access without requiring users to directly log in as root.

### Key Concepts

- `/etc/sudoers`
- `visudo`
- User privileges
- Group privileges
- Least privilege
- Administrative access

Example:

```bash
visudo
```

---

## Linux SSH Authentication

SSH provides secure remote access to Linux servers.

### Key Concepts

- SSH keys
- Public/private key authentication
- `authorized_keys`
- SSH configuration
- Password authentication
- SSH security

Example:

```bash
ssh-keygen
ssh-copy-id user@server
ssh user@server
```

---

# 03. Linux System Configuration

## Linux Banner

Configure login banners to display security or system information when users access a server.

### Key Concepts

- `/etc/motd`
- `/etc/issue`
- SSH login messages
- Security notices

---

## Install a Package

Package management is fundamental to Linux administration.

### Key Concepts

- RPM packages
- `yum`
- `dnf`
- Package installation
- Package removal
- Package updates
- Dependency management

Examples:

```bash
yum install <package>
dnf install <package>
rpm -qa
```

---

## Configure Local Yum Repositories

A local repository allows systems to install packages from an internal package source.

### Key Concepts

- Yum repositories
- Repository configuration
- RPM packages
- Repository metadata
- Offline package installation
- `/etc/yum.repos.d/`

---

## Linux Services

Learn how to manage services using `systemd`.

### Key Concepts

- Start
- Stop
- Restart
- Enable
- Disable
- Status
- Service troubleshooting

Examples:

```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl enable nginx
```

---

# 04. File Search & Text Processing

## Linux Find Command

`find` is used to locate files and directories based on various conditions.

### Key Concepts

- Filename
- File type
- File size
- Ownership
- Permissions
- Modification time
- Execute actions

Examples:

```bash
find /var -name "*.log"

find /tmp -type f

find /var -size +100M
```

---

## Linux String Substitute — sed

`sed` is a powerful stream editor used for searching and modifying text.

### Key Concepts

- Search and replace
- Pattern matching
- Deleting lines
- Printing specific lines
- Editing configuration files

Example:

```bash
sed 's/old/new/g' file.txt
```

---

# 05. Linux Networking & Firewall

## DNS Troubleshooting

DNS troubleshooting is essential when Linux servers cannot resolve hostnames or connect to services by name.

### Key Concepts

- DNS resolution
- `/etc/resolv.conf`
- `/etc/hosts`
- DNS servers
- `dig`
- `nslookup`
- Name resolution troubleshooting

Examples:

```bash
cat /etc/resolv.conf

dig example.com

nslookup example.com
```

---

## Linux Firewalld Setup

Firewalld manages network access using zones, services, and ports.

### Key Concepts

- Firewalld
- Zones
- Services
- Ports
- Runtime rules
- Permanent rules
- Reloading configuration

Examples:

```bash
firewall-cmd --state

firewall-cmd --get-active-zones

firewall-cmd --list-all

firewall-cmd --permanent --add-port=8080/tcp

firewall-cmd --reload
```

---

# 06. Linux Mail Services

## Linux Postfix Mail Server

Postfix is a widely used Mail Transfer Agent (MTA).

### Key Concepts

- SMTP
- Postfix
- Mail queues
- Mail delivery
- Configuration
- Service management

---

## Linux Postfix Troubleshooting

Learn to diagnose mail delivery failures.

### Troubleshooting Areas

- Postfix service
- Configuration errors
- Mail queue
- DNS
- Firewall
- Ports
- Logs
- SMTP connectivity

Useful commands:

```bash
systemctl status postfix

mailq

journalctl -u postfix
```

---

# 07. Load Balancing

## Install and Configure HAProxy LBR

HAProxy can distribute client traffic across multiple backend servers.

### Key Concepts

- Load balancing
- Frontend
- Backend
- Health checks
- Ports
- HAProxy configuration
- High availability concepts

Basic architecture:

```text
              Client
                 │
                 ▼
            ┌─────────┐
            │ HAProxy │
            │   LBR   │
            └────┬────┘
                 │
        ┌────────┴────────┐
        ▼                 ▼
   App Server 1      App Server 2
```

---

## HAProxy LBR Troubleshooting

Learn to troubleshoot load-balancer failures.

### Troubleshooting Areas

- HAProxy service
- Configuration syntax
- Backend availability
- Health checks
- Listening ports
- Firewall
- Network connectivity
- Application availability
- HAProxy logs

---

# 08. Database Troubleshooting

## MariaDB Troubleshooting

Learn the fundamentals of diagnosing MariaDB service and connectivity problems.

### Troubleshooting Areas

- MariaDB service
- Port connectivity
- Authentication
- Users
- Databases
- Configuration
- Logs
- Resource usage

Useful commands:

```bash
systemctl status mariadb

ss -lntp

mysql -u root -p
```

---

# 09. Bash Automation

## Linux Bash Scripts

Bash scripting allows repetitive administrative tasks to be automated.

### Key Concepts

- Variables
- Conditions
- Loops
- Functions
- Arguments
- Exit codes
- Command substitution
- File handling
- Automation

Example:

```bash
#!/bin/bash

echo "Linux automation started"

hostname
date
uptime
```

---

# 10. Web Server Administration

## Add Response Headers in Apache

Learn how to configure Apache HTTP Server to return custom HTTP response headers.

### Key Concepts

- Apache
- HTTP headers
- Server configuration
- Security headers
- Apache modules
- Configuration testing

---

## Apache Troubleshooting

Learn to diagnose common Apache web-server problems.

### Troubleshooting Areas

- Apache service
- Configuration syntax
- Listening ports
- Virtual hosts
- Document root
- File permissions
- Firewall
- Logs

Useful commands:

```bash
systemctl status httpd

httpd -t

ss -lntp

journalctl -u httpd
```

---

# 11. Security

## Linux GPG Encryption

GPG provides encryption and signing capabilities for securing files and data.

### Key Concepts

- Encryption
- Decryption
- Public keys
- Private keys
- Symmetric encryption
- Digital signatures
- Key management

Example:

```bash
gpg --version
```

---

## Application Security

Understand the basic security controls required when running applications on Linux servers.

### Key Concepts

- File permissions
- Service accounts
- Least privilege
- Secure configuration
- Firewall
- SELinux
- SSH security
- Secrets protection
- Application exposure
- Log monitoring

---

# 12. System Maintenance

## Linux LogRotate

Logrotate prevents log files from growing indefinitely and consuming disk space.

### Key Concepts

- Log rotation
- Compression
- Retention
- Rotation frequency
- Log files
- `/etc/logrotate.conf`
- `/etc/logrotate.d/`

Example:

```bash
logrotate --version
```

---

# 13. Configuration Management

## Install Ansible

Ansible is an automation and configuration-management tool widely used in DevOps.

### Key Concepts

- Ansible installation
- Inventory
- Modules
- YAML
- Playbooks
- Remote execution
- SSH-based automation

Example:

```bash
ansible --version
```

---

# 🎯 Level 2 Completion Goals

After completing Level 2, I should be able to:

- [ ] Create and manage Cron jobs
- [ ] Configure collaborative directories
- [ ] Configure sudo access
- [ ] Configure SSH authentication
- [ ] Configure Linux login banners
- [ ] Install and manage packages
- [ ] Configure local Yum repositories
- [ ] Manage Linux services
- [ ] Search files using `find`
- [ ] Manipulate text using `sed`
- [ ] Troubleshoot DNS
- [ ] Configure Firewalld
- [ ] Install and troubleshoot Postfix
- [ ] Configure HAProxy
- [ ] Troubleshoot HAProxy
- [ ] Troubleshoot MariaDB
- [ ] Write Bash scripts
- [ ] Configure Apache
- [ ] Troubleshoot Apache
- [ ] Encrypt data using GPG
- [ ] Configure Logrotate
- [ ] Install and understand Ansible
- [ ] Apply basic application-security principles

---

# 🧪 Troubleshooting Methodology

The most important objective of Level 2 is **not memorizing commands**.

Develop a consistent troubleshooting process:

```text
1. Identify the problem
        ↓
2. Check service status
        ↓
3. Check processes
        ↓
4. Check ports
        ↓
5. Check configuration
        ↓
6. Check logs
        ↓
7. Check permissions
        ↓
8. Check firewall
        ↓
9. Check network connectivity
        ↓
10. Apply the fix
        ↓
11. Test again
        ↓
12. Document the root cause
```

Useful commands to become comfortable with:

```bash
systemctl
journalctl
ps
top
ss
ip
ping
curl
dig
nslookup
grep
sed
awk
find
df
du
free
```

---

# 📊 Level 2 Skill Progression

```text
Linux Fundamentals
       │
       ▼
User & Access Management
       │
       ▼
System Administration
       │
       ▼
Networking & Firewall
       │
       ▼
Services & Web Servers
       │
       ▼
Mail & Database Services
       │
       ▼
Load Balancing
       │
       ▼
Bash Automation
       │
       ▼
Security
       │
       ▼
Configuration Management
       │
       ▼
Production Troubleshooting
```

---
