# Configure SELinux on App Server 2

## 📌 Overview

This task configures **SELinux (Security-Enhanced Linux)** on **App Server 2 (`stapp02`)** in the Stratos Datacenter.

The requirements are:

1. Install the required SELinux packages.
2. Permanently disable SELinux.
3. Do not reboot the server.
4. The SELinux status after the next scheduled reboot must be `disabled`.

---

## 🏗️ Infrastructure

| Server | Hostname | User | Purpose |
|---|---|---|---|
| Application Server 2 | `stapp02` | `steve` | Hosts Nautilus Application 2 |

---

## 🎯 Objective

Install SELinux packages and configure SELinux so that it remains **permanently disabled after the next reboot**.

The server must **not be rebooted manually** as part of this task.

---

## 🔐 What is SELinux?

SELinux stands for **Security-Enhanced Linux**.

It provides an additional security layer using **Mandatory Access Control (MAC)**.

Traditional Linux permissions are based primarily on:

```text
User → Group → Other
```

SELinux adds policy-based restrictions:

```text
Process → SELinux Policy → Resource
```

This can prevent a compromised service from accessing files, ports, or other resources outside its permitted security context.

---

## 🚀 Procedure

### Step 1 — Connect to App Server 2

From the jump host:

```bash
ssh steve@stapp02
```

---

### Step 2 — Switch to root

```bash
sudo -i
```

Verify:

```bash
whoami
```

Expected:

```text
root
```

---

### Step 3 — Check the operating system

```bash
cat /etc/os-release
```

For RHEL/Rocky/Alma/CentOS-based systems, `dnf` or `yum` can be used.

---

### Step 4 — Check existing SELinux packages

```bash
rpm -qa | grep -i selinux
```

---

### Step 5 — Install SELinux packages

Using `dnf`:

```bash
dnf install -y selinux-policy selinux-policy-targeted policycoreutils
```

If `dnf` is unavailable:

```bash
yum install -y selinux-policy selinux-policy-targeted policycoreutils
```

Verify:

```bash
rpm -qa | grep -i selinux
```

---

## ⚙️ Step 6 — Permanently Disable SELinux

Edit:

```bash
vi /etc/selinux/config
```

Find:

```text
SELINUX=enforcing
```

or:

```text
SELINUX=permissive
```

Change it to:

```text
SELINUX=disabled
```

Save and exit:

```text
Esc
:wq
Enter
```

---

## 🔎 Step 7 — Verify Persistent Configuration

Run:

```bash
grep '^SELINUX=' /etc/selinux/config
```

Expected:

```text
SELINUX=disabled
```

This confirms that SELinux is configured to be disabled after reboot.

---

## ⚠️ Step 8 — Do NOT Reboot

Do not run:

```bash
reboot
```

or:

```bash
shutdown -r now
```

The requirement specifically states that a scheduled maintenance reboot will occur later.

---

## 🔍 Understanding `getenforce`

You can check the current runtime state:

```bash
getenforce
```

Before reboot, it may still show:

```text
Enforcing
```

or:

```text
Permissive
```

That does **not** mean the persistent configuration is incorrect.

The important configuration is:

```bash
grep '^SELINUX=' /etc/selinux/config
```

which should show:

```text
SELINUX=disabled
```

After the scheduled reboot, the expected result is:

```bash
getenforce
```

```text
Disabled
```

---

## ✅ Verification Checklist

Run:

```bash
rpm -qa | grep -i selinux
```

Confirm SELinux packages are installed.

Then:

```bash
grep '^SELINUX=' /etc/selinux/config
```

Expected:

```text
SELINUX=disabled
```

Do not reboot.

---

## 🧠 Why This Matters in DevOps

SELinux is commonly encountered when managing:

- RHEL-based production servers
- Apache/Nginx web servers
- Database servers
- Application servers
- Docker/container workloads
- Kubernetes/OpenShift environments
- AWS EC2 Linux instances
- Security-hardened infrastructure
- Compliance-controlled environments

A DevOps engineer needs to understand SELinux because an application can have correct Unix permissions but still be blocked by an SELinux policy.

Useful troubleshooting commands include:

```bash
getenforce
```

```bash
sestatus
```

```bash
ls -Z
```

```bash
ps -eZ
```

```bash
ausearch -m AVC -ts recent
```

---

## 💡 Production Best Practice

Disabling SELinux is generally **not the preferred production security practice**.

A production engineer should normally investigate SELinux denials and fix the underlying policy/context problem instead of simply disabling SELinux.

For example:

```text
Application
    │
    ▼
Permission denied
    │
    ▼
Check Linux permissions
    │
    ▼
Still denied?
    │
    ▼
Check SELinux
    │
    ├── getenforce
    ├── sestatus
    ├── ls -Z
    └── ausearch
    │
    ▼
Fix SELinux context/policy
```

The reason this lab disables SELinux is because **the specific task requirement says to disable it temporarily**.

---

## 📝 Key Commands

```bash
# Connect
ssh steve@stapp02

# Become root
sudo -i

# Check OS
cat /etc/os-release

# Install SELinux packages
dnf install -y selinux-policy selinux-policy-targeted policycoreutils

# Check installed packages
rpm -qa | grep -i selinux

# Edit SELinux configuration
vi /etc/selinux/config

# Verify permanent configuration
grep '^SELINUX=' /etc/selinux/config

# Check current runtime state
getenforce

# Detailed SELinux status
sestatus
```

---

## 🎓 Interview Questions

### 1. What is SELinux?

SELinux is a Linux security mechanism that provides Mandatory Access Control through security policies.

### 2. What are the three SELinux modes?

```text
Enforcing
Permissive
Disabled
```

### 3. What is the difference between Enforcing and Permissive?

**Enforcing** blocks operations that violate SELinux policy.

**Permissive** allows the operation but logs the policy violation.

### 4. What does Disabled mean?

SELinux is not active.

### 5. How do you permanently disable SELinux?

Edit:

```bash
/etc/selinux/config
```

and set:

```text
SELINUX=disabled
```

### 6. Why didn't we reboot?

Because the task explicitly requires no reboot. The configuration is persistent and will take effect during the scheduled reboot.

### 7. Why might `getenforce` still show `Enforcing`?

Because changing `/etc/selinux/config` changes the **persistent configuration**, not necessarily the current running SELinux state.

---

## 📌 Final State

The final configuration on `stapp02` should be:

```text
SELinux packages → Installed
Persistent configuration → SELINUX=disabled
Current server → No reboot performed
Next reboot → SELinux Disabled
```

---

## 🔥 DevOps Takeaway

The important skill here isn't memorizing:

```bash
SELINUX=disabled
```

The real DevOps skill is understanding the difference between:

**runtime state**

and

**persistent system configuration**.

This same concept appears throughout Linux administration:

```text
Current state ≠ Persistent state
```

Always verify both when troubleshooting production systems.