# Disable Direct Root SSH Login on Application Servers

## Lab Overview

As part of a security hardening initiative at **xFusionCorp Industries**, the security team required disabling direct SSH login for the `root` user on all application servers in the **Stratos Datacenter**.

This lab demonstrates how to securely modify the SSH daemon configuration, validate the configuration, restart the SSH service, and verify that direct root SSH access is disabled.

---

## Objective

Disable direct SSH root login on all three application servers:

- `stapp01`
- `stapp02`
- `stapp03`

The required SSH configuration is:

```text
PermitRootLogin no
```

---

## Infrastructure

| Server | Hostname | User | Purpose |
|---|---|---|---|
| Application Server 1 | `stapp01` | `tony` | Nautilus Application 1 |
| Application Server 2 | `stapp02` | `steve` | Nautilus Application 2 |
| Application Server 3 | `stapp03` | `banner` | Nautilus Application 3 |
| Jump Host | `jump-host` | `thor` | Secure access to Stratos DC |

---

## Security Requirement

Direct SSH access using the `root` account must be disabled.

### Before

```text
PermitRootLogin yes
```

### After

```text
PermitRootLogin no
```

This prevents users from directly authenticating as `root` over SSH.

---

# Implementation

## Step 1 — Connect to Application Server 1

From the jump host:

```bash
ssh tony@stapp01
```

Check the current effective SSH configuration:

```bash
sudo sshd -T | grep permitrootlogin
```

Example:

```text
permitrootlogin yes
```

---

## Step 2 — Modify SSH Configuration

Open the SSH daemon configuration:

```bash
sudo vi /etc/ssh/sshd_config
```

Find the following configuration:

```text
PermitRootLogin yes
```

Change it to:

```text
PermitRootLogin no
```

If the directive is commented:

```text
#PermitRootLogin yes
```

uncomment and configure it as:

```text
PermitRootLogin no
```

Save the file.

---

## Step 3 — Validate SSH Configuration

Before restarting SSH, validate the configuration:

```bash
sudo sshd -t
```

### Expected Result

No output indicates that the SSH configuration syntax is valid.

This step is important because restarting SSH with an invalid configuration can cause connectivity problems.

---

## Step 4 — Restart SSH Service

```bash
sudo systemctl restart sshd
```

Check the service:

```bash
sudo systemctl is-active sshd
```

Expected:

```text
active
```

---

## Step 5 — Verify Root Login Restriction

Run:

```bash
sudo sshd -T | grep permitrootlogin
```

Expected:

```text
permitrootlogin no
```

---

# Application Server 2

Connect to the second application server:

```bash
ssh steve@stapp02
```

Check:

```bash
sudo sshd -T | grep permitrootlogin
```

Edit the configuration:

```bash
sudo vi /etc/ssh/sshd_config
```

Set:

```text
PermitRootLogin no
```

Validate:

```bash
sudo sshd -t
```

Restart SSH:

```bash
sudo systemctl restart sshd
```

Verify:

```bash
sudo sshd -T | grep permitrootlogin
```

Expected:

```text
permitrootlogin no
```

---

# Application Server 3

Connect to the third application server:

```bash
ssh banner@stapp03
```

Check:

```bash
sudo sshd -T | grep permitrootlogin
```

Edit:

```bash
sudo vi /etc/ssh/sshd_config
```

Configure:

```text
PermitRootLogin no
```

Validate:

```bash
sudo sshd -t
```

Restart:

```bash
sudo systemctl restart sshd
```

Verify:

```bash
sudo sshd -T | grep permitrootlogin
```

Expected:

```text
permitrootlogin no
```

---

# Final Verification

The effective SSH configuration should be:

| Server | Configuration | Status |
|---|---|---|
| `stapp01` | `permitrootlogin no` | ✅ |
| `stapp02` | `permitrootlogin no` | ✅ |
| `stapp03` | `permitrootlogin no` | ✅ |

---

# Verification Commands

The following commands can be used to verify the implementation:

```bash
sudo sshd -t
```

```bash
sudo systemctl is-active sshd
```

```bash
sudo sshd -T | grep permitrootlogin
```

Expected final output:

```text
permitrootlogin no
```

---

# Security Considerations

Disabling direct root SSH access is a standard Linux security-hardening practice.

### Benefits

- Prevents direct remote authentication as `root`
- Reduces the impact of compromised root credentials
- Forces administrators to authenticate using individual accounts
- Improves accountability through user-level SSH access
- Supports least-privilege administration

Administrators can authenticate using their assigned accounts and use `sudo` when elevated privileges are required.

Example:

```bash
sudo systemctl restart nginx
```

instead of logging in directly as:

```text
root
```

---

# Operational Best Practice

Always validate the SSH configuration before restarting the SSH daemon:

```bash
sudo sshd -t
```

Keep the existing SSH session open until the new configuration has been verified.

A safe workflow is:

```text
CHECK
  ↓
MODIFY
  ↓
VALIDATE
  ↓
RESTART
  ↓
VERIFY
```

---

# Skills Practiced

- Linux system administration
- SSH hardening
- OpenSSH configuration
- Linux security
- Service management with `systemctl`
- Configuration validation
- Remote server administration
- Production-safe change management
- Troubleshooting methodology

---

# Key Commands Learned

```bash
ssh user@server
```

```bash
sudo vi /etc/ssh/sshd_config
```

```bash
sudo sshd -t
```

```bash
sudo sshd -T
```

```bash
sudo systemctl restart sshd
```

```bash
sudo systemctl is-active sshd
```

---

## Result

Direct SSH login for the `root` user was successfully disabled on all application servers in the Stratos Datacenter.

Final configuration:

```text
PermitRootLogin no
```

The SSH daemon configuration was validated, the service was restarted safely, and the effective configuration was verified on all three application servers.