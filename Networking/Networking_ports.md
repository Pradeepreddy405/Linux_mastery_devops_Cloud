# 🛠️ Complete DevOps Port Architecture & Reference Guide

A comprehensive architectural reference guide mapping standard networking ports, communication flows, and multi-tier infrastructure components across modern cloud and DevOps environments[cite: 1].

---

## 📐 Infrastructure Flowchart

```text
┌─────────────────────────────────────────────────┐
│        ADMINISTRATOR / DEVOPS ENGINEER          │
└────────────────────────┬────────────────────────┘
                         │
      ┌──────────────────┼──────────────────┐
      │ (SSH :22)        │ (RDP :3389)      │ (kubectl :6443)
      ▼                  ▼                  ▼
┌───────────┐      ┌───────────┐      ┌───────────┐
│   LINUX   │      │  WINDOWS  │      │  K8s API  │
│  SERVER   │      │   HOST    │      │  SERVER   │
└───────────┘      └───────────┘      └───────────┘

===================================================

                ┌─────────────────┐
                │  USER BROWSER   │
                └────────┬────────┘
                         │ (HTTPS :443)
                         ▼
┌─────────────────────────────────────────────────┐
│ INGRESS / REVERSE PROXY (Nginx / Load Balancer) │
│ Listens: 80, 443  --> Redirects :80 to :443     │
└────────────────────────┬────────────────────────┘
                         │ (HTTP :8080)
                         ▼
┌─────────────────────────────────────────────────┐
│ APPLICATION & CI/CD LAYER                       │
│ - Tomcat / Web App  [:8080]                     │
│ - Jenkins Build UI  [:8080]                     │
│ - Docker Remote API [:2375 Plain / :2376 TLS]   │
└─────┬───────────┬───────────┬───────────┬───────┘
      │ (:3306)   │ (:27017)  │ (:6379)   │ (:9092)
      ▼           ▼           ▼           ▼
┌───────────┐┌───────────┐┌───────────┐┌───────────┐
│   MySQL   ││  MongoDB  ││   Redis   ││   Kafka   │
│ (Rel SQL) ││ (Document)││  (Cache)  ││ (Event)   │
└───────────┘└───────────┘└───────────┘└───────────┘

===================================================
AUXILIARY PIPELINES & OBSERVABILITY
===================================================

[ LOGGING PIPELINE ]
App Logs ───► Logstash/Beats ───► Elasticsearch [:9200]

[ MONITORING PIPELINE ]
Metrics ───► Prometheus [:9090] ───► Grafana [:3000]

[ UTILITY SERVICES ]
FTP ───► [:21 Control Channel]
SMTP ──► [:25 / 587 Email Submission]
```[cite: 1]

---

## 🔐 Layer-by-Layer Port Matrix & Security Guidelines

### 1. Administration & Infrastructure Management
* **Port 22 (SSH):** Secure Shell access for Linux instance administration[cite: 1].
  * *Hardening:* Restrict access to corporate VPN ranges or Bastion Host/Jump Box instances[cite: 1]. Disable password-based root logins[cite: 1].
* **Port 3389 (RDP):** Remote Desktop Protocol for graphical management of Windows instances[cite: 1].
  * *Hardening:* Keep behind private subnets or manage via AWS SSM Fleet Manager[cite: 1].
* **Port 6443 (Kubernetes API Server):** Primary HTTPS API endpoint for `kubectl` cluster control[cite: 1].
  * *Hardening:* Restrict public API server endpoints and enforce Kubernetes RBAC policies[cite: 1].

### 2. Public Edge & Ingress Proxy Layer
* **Port 80 (HTTP) & Port 443 (HTTPS):** Primary entry points for external user web traffic[cite: 1].
  * *Flow Pattern:* Route Port 80 to execute automatic `301 Permanent Redirects` to Port 443[cite: 1].
  * *TLS Termination:* Terminate SSL/TLS certificates at the Application Load Balancer (ALB) or Nginx ingress layer before forwarding internal HTTP requests to backend apps[cite: 1].

### 3. Application Runtimes & CI/CD Pipelines
* **Port 8080 (Web Application Servers & CI Engines):** Default HTTP runtime port for Java Tomcat applications, Node.js/Python microservices, and Jenkins CI/CD servers[cite: 1].
* **Port 2375 / 2376 (Docker Remote API):**
  * **Port 2375 (Unencrypted):** Plaintext TCP socket[cite: 1]. *Never expose publicly to `0.0.0.0/0` as it allows full host root compromise!*[cite: 1]
  * **Port 2376 (Encrypted TLS):** Docker socket secured using mutual TLS client authentication[cite: 1].

### 4. Persistence, Caching & Event Streaming
> ⚠️ **Network Isolation:** All database, caching, and queuing services must reside inside **Private Subnets** with inbound security groups limited strictly to the Application Layer[cite: 1].

* **Port 3306 (MySQL):** Relational database management system (RDBMS) default port[cite: 1].
* **Port 27017 (MongoDB):** Standard connection port for NoSQL document databases[cite: 1].
* **Port 6379 (Redis):** In-memory key-value store utilized for sub-millisecond session caching and rate-limiting[cite: 1].
* **Port 9092 (Kafka):** Distributed high-throughput event streaming broker for microservices architectures[cite: 1].

### 5. Auxiliary Pipelines & Observability
* **Port 9200 (Elasticsearch):** HTTP REST API used by Logstash and Beats for indexing and analyzing logs in the ELK stack[cite: 1].
* **Port 9090 (Prometheus):** Time-series metrics collection engine that scrapes targets (e.g., Node Exporter on Port 9100)[cite: 1].
* **Port 3000 (Grafana):** Metrics visualization UI and alert management platform[cite: 1].
* **Port 21 (FTP):** Legacy file transfer control channel[cite: 1]. Prefer SFTP (Port 22) for cloud deployments[cite: 1].
* **Port 25 / 587 (SMTP):** Mail delivery routing (Port 25) and secure client email submission (Port 587 with STARTTLS)[cite: 1].

---

## 🛡️ DevOps Security Best Practices Checklist

1. **Principle of Least Privilege (PoLP):** Configure default-deny ingress rules across all cloud Security Groups, firewalld, and IPTables rulesets[cite: 1].
2. **Private Subnet Isolation:** Isolate data persistence tiers (MySQL, MongoDB, Redis) from public subnet routing[cite: 1].
3. **Encrypted Transport:** Enforce TLS encryption for external web ingress (443), internal remote engines (Docker 2376), and mail submissions (587)[cite: 1].