# 🐧 Level 3 — Advanced Linux, Networking & Web Services

This level moves beyond basic Linux administration into **production-style web server configuration, networking, authentication, process troubleshooting, secure file transfer, reverse proxies, and SSL/TLS**.

The objective is to develop practical skills that directly support **DevOps, Cloud, SRE, and Linux Administrator** roles.

---

## 📚 Learning Roadmap

```text
LEVEL 3 — ADVANCED LINUX, NETWORKING & WEB SERVICES
│
├── 01. Apache Web Server
│   ├── Apache Redirects
│   ├── Protected Directories in Apache
│   └── PAM Authentication for Apache
│
├── 02. Secure File Transfer
│   └── Install and Configure SFTP
│
├── 03. Application Servers
│   └── Install and Configure Tomcat Server
│
├── 04. Linux Network Services
│   └── Linux Network Services
│
├── 05. Firewall & Network Security
│   └── IPTables Installation and Configuration
│
├── 06. Nginx
│   ├── Nginx as Reverse Proxy
│   └── SSL/TLS for Nginx
│
└── 07. Linux Troubleshooting
    └── Linux Process Troubleshooting
```

---

# 01. Apache Web Server

## Apache Redirects

Learn how to redirect HTTP requests from one URL or path to another.

### Key Concepts

* HTTP redirects
* `301` Permanent Redirect
* `302` Temporary Redirect
* Apache configuration
* `mod_rewrite`
* Virtual Hosts
* URL rewriting

### Example

```apache
Redirect /old-page /new-page
```

### Skills

* Configure Apache redirects
* Test HTTP responses
* Understand redirect status codes
* Troubleshoot redirect configuration

---

## Configure Protected Directories in Apache

Learn how to restrict access to specific directories in Apache.

### Key Concepts

* Directory-based access control
* `.htaccess`
* Authentication
* Authorization
* Apache configuration
* File permissions

### Skills

* Protect sensitive directories
* Configure authentication
* Restrict unauthorized access
* Troubleshoot permission problems

---

## PAM Authentication for Apache

Pluggable Authentication Modules (PAM) provide a centralized authentication framework in Linux.

### Key Concepts

* PAM
* Authentication
* Authorization
* Apache authentication
* PAM configuration
* Linux users

### Skills

* Understand PAM architecture
* Configure Apache authentication
* Integrate Linux users with web authentication
* Troubleshoot authentication failures

---

# 02. Secure File Transfer

## Install and Configure SFTP

SFTP provides secure file transfers over SSH.

### Key Concepts

* SFTP
* SSH
* OpenSSH
* `sshd_config`
* Chroot
* User restrictions
* File permissions

### Example

```bash
sftp user@server
```

### Skills

* Install OpenSSH
* Configure SFTP
* Create SFTP users
* Restrict users to directories
* Configure secure file transfers
* Troubleshoot SFTP connectivity

---

# 03. Application Servers

## Install and Configure Tomcat Server

Apache Tomcat is a Java application server commonly used to host Java web applications.

### Key Concepts

* Tomcat
* Java
* Application server
* WAR files
* `server.xml`
* Ports
* `systemd`
* Application deployment

### Typical Architecture

```text
                 Client
                   │
                   ▼
              ┌─────────┐
              │  Nginx  │
              │  Proxy  │
              └────┬────┘
                   │
                   ▼
              ┌─────────┐
              │ Tomcat  │
              │  :8080  │
              └────┬────┘
                   │
                   ▼
              Java Web App
```

### Skills

* Install Java
* Install Tomcat
* Configure Tomcat
* Change listening ports
* Deploy WAR applications
* Manage Tomcat using systemd
* Troubleshoot Tomcat

---

# 04. Linux Network Services

## Linux Network Services

Develop practical knowledge of Linux networking and network-related services.

### Key Concepts

* IP addressing
* Network interfaces
* Routing
* DNS
* Ports
* TCP/UDP
* Network services
* Connectivity troubleshooting

### Useful Commands

```bash
ip addr
ip route
ss -lntp
ping <host>
curl <url>
dig <domain>
```

### Skills

* Inspect network interfaces
* Understand routing
* Identify listening ports
* Test connectivity
* Troubleshoot network services

---

# 05. Firewall & Network Security

## IPTables Installation and Configuration

IPTables provides host-level packet filtering and firewall control.

### Key Concepts

* IPTables
* Chains
* Rules
* INPUT
* OUTPUT
* FORWARD
* ACCEPT
* DROP
* REJECT
* Ports
* Source IP filtering

### Example

```bash
iptables -L -n -v
```

### Skills

* Install IPTables
* Create firewall rules
* Allow required traffic
* Block unwanted traffic
* Restrict access by source IP
* Persist firewall rules
* Troubleshoot blocked connections

### Security Layers

```text
Cloud Firewall
      ↓
Security Group
      ↓
Host Firewall
      ↓
Application
```

A packet may need to pass through **multiple security layers** before reaching an application.

---

# 06. Nginx

## Linux Nginx as Reverse Proxy

Learn how Nginx receives client requests and forwards them to backend applications.

### Key Concepts

* Reverse proxy
* Upstream servers
* Backend applications
* Proxy headers
* Load balancing
* Ports
* HTTP/HTTPS

### Architecture

```text
             Client
                │
                ▼
          ┌───────────┐
          │   Nginx   │
          │ Reverse   │
          │   Proxy   │
          └─────┬─────┘
                │
        ┌───────┴────────┐
        ▼                ▼
   App Server 1      App Server 2
```

### Example

```nginx
location / {
    proxy_pass http://backend;
}
```

### Skills

* Configure Nginx
* Configure upstream servers
* Proxy requests
* Forward client headers
* Troubleshoot proxy errors
* Understand `502 Bad Gateway`

---

## Setup SSL for Nginx

Learn how to secure web traffic using SSL/TLS.

### Key Concepts

* SSL/TLS
* Certificates
* Private keys
* HTTPS
* Port `443`
* HTTP → HTTPS redirect
* Certificate validation

### Architecture

```text
             HTTPS
               │
               ▼
        ┌─────────────┐
        │    Nginx    │
        │    :443     │
        │  SSL/TLS    │
        └──────┬──────┘
               │
               │ HTTP
               ▼
        ┌─────────────┐
        │   Backend   │
        │ Application  │
        └─────────────┘
```

### Skills

* Generate or use certificates
* Configure private keys
* Enable HTTPS
* Configure port 443
* Redirect HTTP to HTTPS
* Test SSL configuration
* Troubleshoot certificate errors

---

# 07. Linux Troubleshooting

## Linux Process Troubleshooting

Learn to diagnose processes that consume excessive resources or fail unexpectedly.

### Key Concepts

* PID
* Parent/child processes
* CPU usage
* Memory usage
* Process states
* Signals
* Zombie processes
* Resource consumption

### Useful Commands

```bash
ps aux
top
free -h
uptime
kill <PID>
kill -9 <PID>
```

### Troubleshooting Method

```text
Application Problem
       ↓
Check Service
       ↓
Check Process
       ↓
Check CPU / Memory
       ↓
Check Ports
       ↓
Check Logs
       ↓
Identify Root Cause
       ↓
Apply Fix
       ↓
Test
```

---

# 🎯 Level 3 Completion Goals

After completing Level 3, I should be able to:

* [ ] Configure Apache redirects
* [ ] Protect Apache directories
* [ ] Configure Apache authentication
* [ ] Understand PAM authentication
* [ ] Install and configure SFTP
* [ ] Create restricted SFTP users
* [ ] Install and configure Tomcat
* [ ] Deploy applications on Tomcat
* [ ] Understand Linux networking
* [ ] Troubleshoot network connectivity
* [ ] Install and configure IPTables
* [ ] Create host-level firewall rules
* [ ] Configure Nginx as a reverse proxy
* [ ] Understand upstream and backend servers
* [ ] Troubleshoot Nginx `502 Bad Gateway` errors
* [ ] Configure SSL/TLS
* [ ] Enable HTTPS
* [ ] Redirect HTTP to HTTPS
* [ ] Troubleshoot Linux processes

---

# 🧪 Level 3 Troubleshooting Framework

At this level, avoid randomly executing commands.

Follow a structured troubleshooting methodology:

```text
                 PROBLEM
                    │
                    ▼
            ┌───────────────┐
            │ Define Issue  │
            └───────┬───────┘
                    ▼
             Service Status
                    │
                    ▼
              Process Check
                    │
                    ▼
               Port Check
                    │
                    ▼
            Network / DNS
                    │
                    ▼
            Configuration
                    │
                    ▼
               Permissions
                    │
                    ▼
                Firewall
                    │
                    ▼
                  Logs
                    │
                    ▼
              Root Cause
                    │
                    ▼
                  Fix
                    │
                    ▼
                Verify
```

---

# 🛠️ Commands to Master

```bash
# Service Management
systemctl
journalctl

# Process Management
ps
top
kill

# Networking
ip
ss
ping
curl
dig

# Firewall
iptables
firewall-cmd

# Text Processing
grep
sed

# File Management
find
chmod
chown
```

---

# 🏗️ Production Architecture Concepts

Level 3 should help me understand how individual Linux components work together.

```text
                         Internet
                            │
                            ▼
                    ┌──────────────┐
                    │   Firewall   │
                    └───────┬──────┘
                            │
                            ▼
                    ┌──────────────┐
                    │    Nginx     │
                    │ Reverse Proxy│
                    │    HTTPS     │
                    └───────┬──────┘
                            │
                            ▼
                    ┌──────────────┐
                    │    Tomcat    │
                    │ Application  │
                    └───────┬──────┘
                            │
                            ▼
                    ┌──────────────┐
                    │   Database   │
                    └──────────────┘
```

The objective is to understand:

* Where a request enters the infrastructure
* Which component handles the request
* Where the request can fail
* Which logs to inspect
* Which ports are involved
* Which firewall rules apply
* How to identify the root cause

---

# 📊 Level 3 Skill Progression

```text
Level 1
Linux Fundamentals
       │
       ▼
Level 2
Linux Administration
       │
       ▼
Level 3
Advanced Linux
       │
       ├── Apache
       ├── Nginx
       ├── Tomcat
       ├── SFTP
       ├── Networking
       ├── IPTables
       ├── PAM
       ├── SSL/TLS
       └── Troubleshooting
       │
       ▼
Level 4
DevOps Infrastructure
```

---

