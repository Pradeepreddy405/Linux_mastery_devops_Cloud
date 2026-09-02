# Configure GUI Boot as Default on App Servers

## 📌 Task Description

The `Stratos Datacenter` has several App Servers where newly installed tools require graphical user interface (GUI) access.

The task is to configure the **default systemd target** on all App Servers so that the servers boot into **GUI mode by default**.

> ⚠️ **Important:** Do not reboot any server after making the configuration change.

---

## 🎯 Objective

Change the default boot target from:

```bash
multi-user.target
```

to:

```bash
graphical.target
```

This ensures that when the server is **next rebooted**, it will boot into the graphical environment.

---

## 🧠 Concepts

Modern Linux distributions using `systemd` use **targets** instead of the traditional SysV init runlevels.

### Common systemd Targets

| Target | Purpose |
|---|---|
| `poweroff.target` | Shut down the system |
| `rescue.target` | Single-user/recovery environment |
| `multi-user.target` | Multi-user command-line/server environment |
| `graphical.target` | Multi-user environment with GUI |
| `reboot.target` | Reboot the system |

### Important Targets

#### `multi-user.target`

Normally used for Linux servers:

```text
Boot
  ↓
systemd
  ↓
multi-user.target
  ↓
Command-line environment
```

#### `graphical.target`

Used when a graphical environment is required:

```text
Boot
  ↓
systemd
  ↓
graphical.target
  ↓
GUI login
```

---

# 🔧 Implementation

## Step 1: Connect to the App Server

SSH into the required App Server.

Example:

```bash
ssh user@stapp01
```

Repeat the process for all required App Servers.

---

## Step 2: Check the Current Default Target

Run:

```bash
systemctl get-default
```

Example output:

```text
multi-user.target
```

This means the server is currently configured to boot into the command-line/server environment.

---

## Step 3: Configure GUI as the Default Target

Run:

```bash
sudo systemctl set-default graphical.target
```

Expected output will be similar to:

```text
Removed "/etc/systemd/system/default.target".
Created symlink /etc/systemd/system/default.target → /usr/lib/systemd/system/graphical.target.
```

The exact path may differ depending on the Linux distribution.

---

## Step 4: Verify the Configuration

Run:

```bash
systemctl get-default
```

Expected output:

```text
graphical.target
```

This confirms that GUI mode is now configured as the default boot target.

---

# ⚠️ Do NOT Reboot

The task explicitly requires that the server **must not be rebooted**.

Do **not** run:

```bash
reboot
```

or:

```bash
systemctl reboot
```

or:

```bash
shutdown -r now
```

The `set-default` command only changes the configuration for the **next boot**.

The currently running server will continue operating normally.

---

# 🔍 Useful Verification Commands

### List available targets

```bash
systemctl list-unit-files --type=target
```

### Check default target

```bash
systemctl get-default
```

### Check active targets

```bash
systemctl list-units --type=target
```

### View dependencies of graphical target

```bash
systemctl list-dependencies graphical.target
```

### View dependencies of multi-user target

```bash
systemctl list-dependencies multi-user.target
```

---

# 🔄 How to Revert the Change

If GUI boot is no longer required, change the default back to the normal server target:

```bash
sudo systemctl set-default multi-user.target
```

Verify:

```bash
systemctl get-default
```

Expected:

```text
multi-user.target
```

---

# 📝 Runbook

For each App Server:

```bash
# 1. Check current target
systemctl get-default

# 2. Set GUI as default
sudo systemctl set-default graphical.target

# 3. Verify
systemctl get-default

# 4. DO NOT reboot
```

Expected final result:

```text
graphical.target
```

---

# ✅ Validation Checklist

- [ ] Connected to every required App Server
- [ ] Checked the current default target
- [ ] Changed default target to `graphical.target`
- [ ] Verified using `systemctl get-default`
- [ ] Did not reboot any server
- [ ] Configuration is persistent for the next boot

---

## 💡 Key Takeaway

The most important command for this task is:

```bash
systemctl set-default graphical.target
```

It creates/changes the systemd `default.target` symlink so that **the next time Linux boots, it will target the graphical environment by default**.

It does **not** immediately change the currently running system into GUI mode.