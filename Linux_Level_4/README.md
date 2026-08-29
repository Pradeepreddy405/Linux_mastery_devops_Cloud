# 🐧 Level 4 — Linux Application Stack & Infrastructure Services

This level focuses on building and troubleshooting a complete Linux-based application stack.

The objective is to move from individual Linux services to **multi-tier application infrastructure**, including **Nginx load balancing, web applications, PHP-FPM, Unix sockets, PostgreSQL, databases, and Bash automation**.

These skills form an important foundation for **DevOps, Cloud, SRE, and Infrastructure Engineering**.

---

# 📚 Learning Roadmap

```text
LEVEL 4 — LINUX APPLICATION STACK & INFRASTRUCTURE SERVICES
│
├── 01. Load Balancing
│   └── Install and Configure Nginx as an LBR
│
├── 02. Database Administration
│   ├── Install and Configure PostgreSQL
│   └── Install and Configure DB Server
│
├── 03. Bash Automation
│   └── Bash Scripts — if/else Statements
│
├── 04. Web Application Infrastructure
│   ├── Install and Configure Web Application
│   └── Install and Configure PHP-FPM
│
└── 05. Nginx + PHP Integration
    └── Configure Nginx + PHP-FPM Using Unix Socket
```

---

# 01. Load Balancing

## Install and Configure Nginx as an LBR

Nginx can operate as a **Load Balancer (LBR)** and distribute incoming traffic across multiple backend servers.

### Key Concepts

* Load balancing
* Nginx
* Upstream servers
* Backend servers
* Health checks
* Reverse proxy
* HTTP traffic
* Ports

### Architecture

```text
                         Client
                            │
                            ▼
                    ┌──────────────┐
                    │     Nginx    │
                    │     LBR      │
                    └───────┬──────┘
                            │
                ┌───────────┴───────────┐
                ▼                       ▼
        ┌──────────────┐        ┌──────────────┐
        │ App Server 1 │        │ App Server 2 │
        │     :8080    │        │     :8080    │
        └──────────────┘        └──────────────┘
```

### Common Load-Balancing Methods

* Round Robin
* Least Connections
* IP Hash

### Skills

* Install Nginx
* Configure Nginx as an LBR
* Configure upstream backends
* Distribute traffic
* Verify backend connectivity
* Troubleshoot failed backend servers
* Understand reverse proxy vs load balancer

---

# 02. Database Administration

## Install and Configure PostgreSQL

PostgreSQL is a powerful open-source relational database system.

### Key Concepts

* PostgreSQL
* Database
* Database users
* Authentication
* Roles
* Permissions
* PostgreSQL service
* Database connectivity
* Ports

### Useful Commands

```bash
systemctl status postgresql

psql --version

sudo -u postgres psql
```

### Skills

* Install PostgreSQL
* Start and enable PostgreSQL
* Create databases
* Create users
* Configure authentication
* Grant permissions
* Test database connectivity
* Troubleshoot PostgreSQL

---

## Install and Configure DB Server

Learn the fundamentals of deploying a database server for an application.

### Key Concepts

* Database server
* Database users
* Database authentication
* Network connectivity
* Database ports
* Service management
* Application-to-database communication

### Architecture

```text
                Web Application
                       │
                       │ Database Connection
                       ▼
                ┌──────────────┐
                │  DB Server   │
                │ PostgreSQL   │
                └──────────────┘
```

### Skills

* Configure a database server
* Create application databases
* Create application users
* Configure access
* Test connectivity
* Troubleshoot database connections

---

# 03. Bash Automation

## Bash Scripts — if/else Statements

Conditional statements allow Bash scripts to make decisions based on conditions.

### Key Concepts

* Bash scripting
* Variables
* Conditions
* `if`
* `elif`
* `else`
* Comparison operators
* Exit status
* Command evaluation

### Example

```bash
#!/bin/bash

if [ "$1" = "start" ]; then
    echo "Starting application"
else
    echo "Invalid option"
fi
```

### Skills

* Write Bash scripts
* Use variables
* Implement conditions
* Check command exit status
* Automate administrative tasks
* Build decision-making scripts

---

# 04. Web Application Infrastructure

## Install and Configure Web Application

Learn how to deploy and configure an application on a Linux server.

### Key Concepts

* Web application
* Application server
* Web server
* Document root
* Application configuration
* Ports
* Permissions
* Service management

### Basic Architecture

```text
                         Client
                            │
                            ▼
                     ┌───────────┐
                     │   Nginx   │
                     └─────┬─────┘
                           │
                           ▼
                    ┌─────────────┐
                    │ Application │
                    │   Server    │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  Database   │
                    └─────────────┘
```

### Skills

* Install application dependencies
* Configure application files
* Configure application ports
* Manage application services
* Configure permissions
* Test application availability
* Troubleshoot application failures

---

## Install and Configure PHP-FPM

PHP-FPM (FastCGI Process Manager) is commonly used to execute PHP applications behind Nginx.

### Key Concepts

* PHP
* PHP-FPM
* FastCGI
* Process pools
* PHP configuration
* Unix sockets
* TCP sockets
* Nginx integration

### Architecture

```text
Client
  │
  ▼
Nginx
  │
  │ FastCGI
  ▼
PHP-FPM
  │
  ▼
PHP Application
```

### Skills

* Install PHP
* Install PHP-FPM
* Configure PHP-FPM
* Manage PHP-FPM service
* Configure PHP-FPM pools
* Understand FastCGI
* Troubleshoot PHP-FPM

---

# 05. Nginx + PHP Integration

## Configure Nginx + PHP-FPM Using Unix Socket

Configure Nginx to communicate with PHP-FPM through a **Unix domain socket**.

### Key Concepts

* Unix socket
* PHP-FPM
* FastCGI
* Nginx
* `fastcgi_pass`
* Socket permissions
* PHP processing

### Architecture

```text
                    Client
                       │
                       ▼
                 ┌──────────┐
                 │  Nginx   │
                 │   :80    │
                 └────┬─────┘
                      │
                      │ Unix Socket
                      │
                      ▼
            ┌──────────────────┐
            │     PHP-FPM      │
            │ default.sock     │
            └────────┬─────────┘
                     │
                     ▼
                PHP Application
```

### Example Configuration

```nginx
location ~ \.php$ {
    include fastcgi_params;
    fastcgi_pass unix:/var/run/php-fpm/default.sock;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}
```

### Skills

* Configure PHP-FPM Unix sockets
* Configure Nginx FastCGI
* Understand socket permissions
* Configure PHP application processing
* Troubleshoot `502 Bad Gateway`
* Troubleshoot PHP-FPM
* Verify Unix socket connectivity

---

# 🔍 PHP-FPM Troubleshooting

When Nginx cannot communicate with PHP-FPM, check the following:

```text
Nginx
  │
  ├── Configuration
  │
  ├── PHP-FPM Service
  │
  ├── Unix Socket
  │
  ├── Socket Permissions
  │
  ├── PHP-FPM Pool
  │
  └── SELinux
```

### Useful Commands

```bash
systemctl status nginx

systemctl status php-fpm

php-fpm -t

ls -l /var/run/php-fpm/

ss -lx

journalctl -u php-fpm

journalctl -u nginx
```

---

# 🏗️ Complete Application Architecture

Level 4 brings the individual services together into a multi-tier architecture:

```text
                              Internet
                                  │
                                  ▼
                         ┌────────────────┐
                         │     Nginx      │
                         │      LBR       │
                         └───────┬────────┘
                                 │
                    ┌────────────┴────────────┐
                    │                         │
                    ▼                         ▼
             ┌──────────────┐          ┌──────────────┐
             │ Web Server 1 │          │ Web Server 2 │
             │    Nginx     │          │    Nginx     │
             └──────┬───────┘          └──────┬───────┘
                    │                         │
                    ▼                         ▼
             ┌──────────────┐          ┌──────────────┐
             │  PHP-FPM     │          │  PHP-FPM     │
             │ Unix Socket  │          │ Unix Socket  │
             └──────┬───────┘          └──────┬───────┘
                    │                         │
                    └────────────┬────────────┘
                                 │
                                 ▼
                         ┌──────────────┐
                         │  PostgreSQL  │
                         │   Database   │
                         └──────────────┘
```

This architecture represents a basic **three-tier application environment**:

```text
Presentation Layer
        ↓
Application Layer
        ↓
Database Layer
```

---

# 🎯 Level 4 Completion Goals

After completing Level 4, I should be able to:

* [ ] Install and configure Nginx as a Load Balancer
* [ ] Configure Nginx upstream servers
* [ ] Understand load-balancing concepts
* [ ] Install and configure PostgreSQL
* [ ] Create PostgreSQL users and databases
* [ ] Configure database authentication
* [ ] Configure a database server
* [ ] Write Bash scripts using `if/else`
* [ ] Deploy a Linux-based web application
* [ ] Configure application services
* [ ] Install PHP-FPM
* [ ] Understand PHP-FPM process pools
* [ ] Configure Nginx with PHP-FPM
* [ ] Configure PHP-FPM using Unix sockets
* [ ] Understand FastCGI
* [ ] Troubleshoot PHP-FPM
* [ ] Troubleshoot Nginx `502 Bad Gateway`
* [ ] Troubleshoot Unix socket permissions
* [ ] Understand multi-tier application architecture

---

# 🧪 Level 4 Troubleshooting Framework

When an application is not working, troubleshoot from the outside in:

```text
                     User Request
                          │
                          ▼
                       Nginx LBR
                          │
                    ┌─────┴─────┐
                    ▼           ▼
                Web Server   Web Server
                    │           │
                    ▼           ▼
                 PHP-FPM     PHP-FPM
                    │           │
                    └─────┬─────┘
                          ▼
                      Database
```

For each layer check:

```text
1. Is the service running?
2. Is the process running?
3. Is the port listening?
4. Is the configuration correct?
5. Are permissions correct?
6. Is the firewall blocking traffic?
7. Is SELinux blocking access?
8. Are the logs showing an error?
9. Can the next layer communicate?
10. Can the complete application request succeed?
```

---

# 🛠️ Commands to Master

### Nginx

```bash
nginx -t
systemctl status nginx
systemctl restart nginx
```

### PHP-FPM

```bash
php-fpm -t
systemctl status php-fpm
systemctl restart php-fpm
```

### PostgreSQL

```bash
psql --version
systemctl status postgresql
```

### Networking

```bash
ip addr
ss -lntp
ss -lx
curl
ping
```

### Troubleshooting

```bash
journalctl
grep
tail
ls -l
ps
top
```

### Bash

```bash
bash script.sh
chmod +x script.sh
```

---

# 📊 Level 4 Skill Progression

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
Advanced Linux & Networking
       │
       ▼
Level 4
Linux Application Stack
       │
       ├── Nginx LBR
       ├── Web Applications
       ├── PHP-FPM
       ├── Unix Sockets
       ├── PostgreSQL
       ├── Database Servers
       └── Bash Automation
       │
       ▼
Level 5
DevOps Infrastructure & Automation
```

---

# 🚀 Next Level

After completing Level 4, the next logical step is:

## Level 5 — DevOps Infrastructure & Automation

### Focus Areas

* Git & GitHub
* Docker
* Kubernetes
* Jenkins
* GitHub Actions
* Terraform
* Ansible
* AWS
* CI/CD
* Infrastructure as Code
* Monitoring
* Prometheus
* Grafana
* DevSecOps

---

# 📈 Progress

| Category              | Details                                           |
| --------------------- | ------------------------------------------------- |
| **Level**             | Level 4                                           |
| **Focus**             | Linux Application Stack & Infrastructure Services |
| **Tasks**             | 7                                                 |
| **Experience Points** | —                                                 |
| **Status**            | 🚧 In Progress                                    |
| **Target Roles**      | DevOps / Cloud / SRE / Infrastructure Engineer    |

---

