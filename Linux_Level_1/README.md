# 🐧 Level 1 — Linux Fundamentals

This repository contains my **Level 1 Linux Fundamentals** learning path, focused on building a strong foundation in Linux administration, system management, security, and troubleshooting.

The goal is to understand the **commands, concepts, and practical administration tasks** required for DevOps and Cloud engineering.

---

## 📚 Learning Roadmap

```text
LEVEL 1 — LINUX FUNDAMENTALS
│
├── 01. User Management
│   ├── useradd
│   ├── usermod
│   ├── userdel
│   ├── passwd
│   └── chage
│
├── 02. Group Management
│   ├── groupadd
│   ├── groupmod
│   └── usermod -aG
│
├── 03. File & Directory Management
│   ├── cp
│   ├── mv
│   ├── rm
│   ├── mkdir
│   └── find
│
├── 04. Permissions
│   ├── chmod
│   ├── chown
│   └── chgrp
│
├── 05. SSH & File Transfer
│   ├── ssh
│   ├── ssh-keygen
│   ├── ssh-copy-id
│   └── scp
│
├── 06. Text Processing
│   ├── grep
│   ├── sed
│   └── awk
│
├── 07. Scheduling
│   ├── cron
│   ├── crontab
│   └── cron access control
│
├── 08. Services & Systemd
│   ├── systemctl
│   ├── service
│   └── systemd targets
│
├── 09. System Configuration
│   ├── timedatectl
│   └── hostnamectl
│
├── 10. Processes & Limits
│   ├── ps
│   ├── top
│   ├── kill
│   └── ulimit
│
├── 11. Firewall
│   ├── firewall-cmd
│   ├── zones
│   └── ports/services
│
└── 12. Security
    ├── SELinux
    ├── SSH hardening
    └── Access control
```

---

# 01. User Management

Linux user management is the foundation of system administration.

### Commands Covered

| Command | Purpose |
|---|---|
| `useradd` | Create a new user |
| `usermod` | Modify an existing user |
| `userdel` | Delete a user |
| `passwd` | Set or change a user's password |
| `chage` | Manage password aging and user expiration |

### Key Concepts

- Creating users
- Modifying users
- Deleting users
- Setting passwords
- User expiration
- Login shells
- Home directories
- System/service users

---

# 02. Group Management

Groups are used to manage permissions and access for multiple users.

### Commands Covered

| Command | Purpose |
|---|---|
| `groupadd` | Create a group |
| `groupmod` | Modify a group |
| `usermod -aG` | Add a user to a supplementary group |

### Key Concepts

- Primary groups
- Supplementary groups
- Group membership
- Group-based access control
- `/etc/group`
- `/etc/gshadow`

---

# 03. File & Directory Management

Linux administrators constantly work with files and directories.

### Commands Covered

| Command | Purpose |
|---|---|
| `cp` | Copy files/directories |
| `mv` | Move or rename files/directories |
| `rm` | Remove files/directories |
| `mkdir` | Create directories |
| `find` | Search for files/directories |

### Key Concepts

- Absolute vs relative paths
- Creating directories
- Copying files
- Moving files
- Removing files
- Searching the filesystem
- Recursive operations

---

# 04. Permissions

Linux permissions control who can read, write, or execute files and directories.

### Commands Covered

| Command | Purpose |
|---|---|
| `chmod` | Change permissions |
| `chown` | Change ownership |
| `chgrp` | Change group ownership |

### Permission Model

```text
          User   Group   Others
           │       │       │
           ▼       ▼       ▼
         rwx     rwx     rwx

Example:

-rwxr-xr--
 │  │  │
 │  │  └── Others
 │  └───── Group
 └──────── User
```

### Key Concepts

- Read (`r`)
- Write (`w`)
- Execute (`x`)
- User ownership
- Group ownership
- Numeric permissions
- Symbolic permissions
- Recursive permissions

Example:

```bash
chmod 755 script.sh
chown user1:user1 file.txt
chgrp developers project/
```

---

# 05. SSH & File Transfer

SSH provides secure remote access to Linux servers and is heavily used in DevOps.

### Commands Covered

| Command | Purpose |
|---|---|
| `ssh` | Connect to a remote server |
| `ssh-keygen` | Generate SSH keys |
| `ssh-copy-id` | Copy public key to a remote server |
| `scp` | Securely copy files |

### Key Concepts

- SSH authentication
- Password authentication
- SSH key authentication
- Public/private keys
- Remote server access
- Secure file transfer
- SSH configuration

Example:

```bash
ssh user@server

ssh-keygen

ssh-copy-id user@server

scp file.txt user@server:/tmp/
```

---

# 06. Text Processing

Text processing is essential for Linux administration, log analysis, scripting, and troubleshooting.

### Commands Covered

| Command | Purpose |
|---|---|
| `grep` | Search text |
| `sed` | Search and modify text |
| `awk` | Process structured text |

### Key Concepts

- Searching logs
- Filtering output
- Pattern matching
- Text replacement
- Field extraction
- Basic regular expressions

Examples:

```bash
grep "error" application.log

sed 's/old/new/g' file.txt

awk '{print $1}' access.log
```

---

# 07. Scheduling

Linux provides scheduling mechanisms for executing commands and scripts automatically.

### Commands & Concepts

```text
cron
crontab
cron access control
```

### Key Concepts

- Cron jobs
- User crontabs
- System cron jobs
- Scheduling commands
- `/etc/crontab`
- `/etc/cron.d/`
- `/etc/cron.allow`
- `/etc/cron.deny`

Example:

```bash
crontab -e
```

Cron format:

```text
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week
│ │ │ └──── Month
│ │ └────── Day of month
│ └──────── Hour
└────────── Minute
```

---

# 08. Services & Systemd

`systemd` manages services and system states on modern Linux distributions.

### Commands Covered

| Command | Purpose |
|---|---|
| `systemctl` | Manage systemd services |
| `service` | Manage services |
| `systemd targets` | Control system operating states |

### Common Commands

```bash
systemctl status nginx

systemctl start nginx

systemctl stop nginx

systemctl restart nginx

systemctl enable nginx

systemctl disable nginx
```

### Key Concepts

- Starting services
- Stopping services
- Restarting services
- Service status
- Enable/disable at boot
- Systemd targets
- Service troubleshooting

---

# 09. System Configuration

Basic system configuration is an important Linux administration skill.

### Commands Covered

| Command | Purpose |
|---|---|
| `timedatectl` | Manage system date, time, and timezone |
| `hostnamectl` | Manage system hostname |

### Examples

```bash
timedatectl

timedatectl list-timezones

timedatectl set-timezone Asia/Kolkata

hostnamectl

hostnamectl set-hostname server01
```

### Key Concepts

- System timezone
- System clock
- NTP
- Hostname
- Server identification

---

# 10. Processes & Limits

Understanding processes is critical for troubleshooting Linux servers.

### Commands Covered

| Command | Purpose |
|---|---|
| `ps` | Display running processes |
| `top` | Monitor processes and resource usage |
| `kill` | Terminate processes |
| `ulimit` | Configure user resource limits |

### Examples

```bash
ps aux

top

kill <PID>

ulimit -a
```

### Key Concepts

- Process IDs
- Parent/child processes
- CPU usage
- Memory usage
- Process termination
- Resource limits
- Open files
- Maximum processes

---

# 11. Firewall

Linux firewall configuration controls network traffic to and from servers.

### Topics Covered

```text
firewall-cmd
zones
ports
services
```

### Common Commands

```bash
firewall-cmd --state

firewall-cmd --get-active-zones

firewall-cmd --list-all

firewall-cmd --permanent --add-port=8080/tcp

firewall-cmd --reload
```

### Key Concepts

- Firewall zones
- Inbound traffic
- Ports
- Services
- Permanent rules
- Runtime rules
- Firewall troubleshooting

---

# 12. Security

Linux security fundamentals are essential before moving into advanced DevOps and Cloud security.

### Topics Covered

```text
SELinux
SSH Hardening
Access Control
```

### SELinux

Key concepts:

- SELinux modes
- Enforcing
- Permissive
- Disabled
- Security contexts
- SELinux policies

Useful commands:

```bash
getenforce

sestatus

setenforce 0

setenforce 1
```

### SSH Hardening

Important areas:

- Disable unnecessary root SSH access
- Use SSH key authentication
- Restrict SSH access
- Secure SSH configuration
- Understand `/etc/ssh/sshd_config`

### Access Control

Understand:

- Linux users
- Linux groups
- File permissions
- Ownership
- SSH access
- SELinux controls

---

# 🎯 Level 1 Completion Goals

By completing Level 1, I should be able to:

- [ ] Create and manage Linux users
- [ ] Create and manage groups
- [ ] Manage files and directories
- [ ] Understand Linux permissions
- [ ] Change file ownership
- [ ] Connect to servers using SSH
- [ ] Transfer files securely
- [ ] Search and manipulate text
- [ ] Create and manage cron jobs
- [ ] Manage Linux services
- [ ] Understand systemd
- [ ] Configure hostname and timezone
- [ ] Monitor and manage processes
- [ ] Configure resource limits
- [ ] Configure basic firewall rules
- [ ] Understand SELinux fundamentals
- [ ] Apply basic SSH security practices

---

# 🧪 Practical Lab Focus

This level should not be treated as a list of commands to memorize.

For every topic, practice:

```text
Learn the concept
      ↓
Understand the command
      ↓
Execute it in a Linux lab
      ↓
Break something intentionally
      ↓
Troubleshoot it
      ↓
Document the solution
```

The objective is to become comfortable administering a Linux server from the command line.

---

