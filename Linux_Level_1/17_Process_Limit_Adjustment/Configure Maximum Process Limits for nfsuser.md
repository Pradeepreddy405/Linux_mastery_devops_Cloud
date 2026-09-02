# Configure Maximum Process Limits for `nfsuser`

## 📌 Task Overview

The `Nautilus` application server was experiencing performance degradation because the `nfsuser` user was holding an excessive number of processes.

To prevent a single user from consuming excessive process resources, configure Linux process limits for `nfsuser` on **Application Server 1 (`stapp01`)**.

### Requirements

| Limit | Required Value |
|---|---:|
| Soft process limit (`nproc`) | `1027` |
| Hard process limit (`nproc`) | `2024` |

---

## 🖥️ Infrastructure

| Server | Hostname | User |
|---|---|---|
| Application Server 1 | `stapp01` | `tony` |

Target user:

```text
nfsuser
```

---

# 🔧 Implementation

## 1. Connect to App Server 1

From the jump host:

```bash
ssh tony@stapp01
```

Verify the hostname and user:

```bash
hostname
whoami
```

Expected:

```text
stapp01
tony
```

---

## 2. Become Root

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

## 3. Verify `nfsuser` Exists

```bash
id nfsuser
```

Example:

```text
uid=xxxx(nfsuser) gid=xxxx(nfsuser) groups=xxxx(nfsuser)
```

---

## 4. Check Existing Process Limits

Check whether `nfsuser` already has an `nproc` configuration:

```bash
grep -R "^nfsuser.*nproc" /etc/security/limits.conf /etc/security/limits.d/ 2>/dev/null
```

Also check the main limits file:

```bash
grep -n "nfsuser" /etc/security/limits.conf
```

---

## 5. Configure the Process Limits

Edit the limits configuration:

```bash
vi /etc/security/limits.conf
```

Add:

```text
nfsuser soft nproc 1027
nfsuser hard nproc 2024
```

Save and exit.

### Configuration Explanation

```text
nfsuser soft nproc 1027
```

Sets the **soft limit** to `1027`.

```text
nfsuser hard nproc 2024
```

Sets the **hard limit** to `2024`.

`nproc` represents the maximum number of processes available to the user.

---

# 🔍 Verification

## 6. Verify the Configuration File

Run:

```bash
grep -n "nfsuser.*nproc" /etc/security/limits.conf
```

Expected:

```text
nfsuser soft nproc 1027
nfsuser hard nproc 2024
```

---

## 7. Check for Conflicting Rules

Run:

```bash
grep -R "nfsuser.*nproc" /etc/security/limits.conf /etc/security/limits.d/ 2>/dev/null
```

Make sure there are no conflicting `nproc` settings for `nfsuser`.

---

## 8. Start a New `nfsuser` Session

The limits are normally applied when a **new login session** is created.

From the root shell:

```bash
su - nfsuser
```

Verify:

```bash
whoami
```

Expected:

```text
nfsuser
```

---

## 9. Verify the Soft Limit

Run:

```bash
ulimit -Su
```

Expected:

```text
1027
```

---

## 10. Verify the Hard Limit

Run:

```bash
ulimit -Hu
```

Expected:

```text
2024
```

---

## 11. Verify Both Limits Together

Run:

```bash
echo "Soft limit: $(ulimit -Su)"
echo "Hard limit: $(ulimit -Hu)"
```

Expected:

```text
Soft limit: 1027
Hard limit: 2024
```

You can also inspect all limits:

```bash
ulimit -a
```

Look for:

```text
max user processes
```

---

# 📁 Important Configuration Files

### Main configuration

```text
/etc/security/limits.conf
```

### Additional configuration directory

```text
/etc/security/limits.d/
```

### Search for process limits

```bash
grep -R "nproc" /etc/security/limits.conf /etc/security/limits.d/ 2>/dev/null
```

---

# 🧠 What Is `nproc`?

`nproc` controls the number of processes that a user can create/use.

For this task:

```text
                    nfsuser
                       │
                       ▼
                    nproc
                  /       \
                 /         \
        Soft Limit       Hard Limit
           1027             2024
```

The soft limit is the normal enforced limit for the session, while the hard limit represents the maximum ceiling that the soft limit can generally be raised to.

---

# 🌎 Real-World DevOps Use Case

Process limits are useful when multiple applications or users share a Linux server.

For example:

```text
Production Server
│
├── application1 → appuser
├── application2 → appuser2
├── Jenkins      → jenkins
├── Monitoring   → monitor
└── Backup       → backup
```

If one application starts creating excessive processes, it can consume system resources and affect other workloads.

An `nproc` limit provides an additional protection mechanism.

Common use cases include:

- Preventing runaway applications
- Protecting shared Linux servers
- Limiting CI/CD workloads
- Reducing impact of fork bombs
- Multi-user environments
- Basic Linux resource isolation

For modern services, **systemd limits and cgroups** are often preferable for service-level resource control, while Kubernetes generally uses **container resource limits and cgroups**.

---

# ⚠️ Important Notes

- No server reboot is required.
- No service restart is normally required.
- `/etc/security/limits.conf` affects **new PAM sessions**.
- Existing sessions may continue using their previous limits.
- Always verify with `ulimit`.
- Don't create `nfsuser` unless the task specifically requires it.
- Check `/etc/security/limits.d/` for conflicting configurations.

---

# ✅ Final Verification

Run:

```bash
grep -R "nfsuser.*nproc" /etc/security/limits.conf /etc/security/limits.d/ 2>/dev/null
```

Then create a new `nfsuser` session:

```bash
su - nfsuser
```

Finally:

```bash
echo "Soft limit: $(ulimit -Su)"
echo "Hard limit: $(ulimit -Hu)"
```

Expected:

```text
Soft limit: 1027
Hard limit: 2024
```

---

# 🎯 Final Configuration

```text
User: nfsuser

Soft nproc limit: 1027
Hard nproc limit: 2024
```

The task is successfully completed when the configuration exists and a **new `nfsuser` session** reports:

```text
Soft limit: 1027
Hard limit: 2024
```