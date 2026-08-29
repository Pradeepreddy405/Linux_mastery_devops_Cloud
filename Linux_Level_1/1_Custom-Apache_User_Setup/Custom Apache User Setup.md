# 👤 Custom Apache User Setup — `yousuf`

## 📌 Task Overview

The `xFusionCorp Industries` security team requires custom Linux users for web applications to improve application-level security.

The objective is to create a custom user on **Application Server 3** with a specific UID and home directory.

### Requirements

| Requirement | Value |
|---|---|
| Server | App Server 3 |
| Hostname | `stapp03` |
| Username | `yousuf` |
| UID | `1863` |
| Home Directory | `/var/www/yousuf` |

---

# 🏗️ Infrastructure

| Server | Hostname | Login User |
|---|---|---|
| Application Server 1 | `stapp01` | `tony` |
| Application Server 2 | `stapp02` | `steve` |
| **Application Server 3** | **`stapp03`** | **`banner`** |
| Load Balancer | `stlb01` | `loki` |
| Database Server | `stdb01` | `peter` |
| Storage Server | `ststor01` | `natasha` |
| Backup Server | `stbkp01` | `clint` |
| Mail Server | `stmail01` | `groot` |
| Jump Host | `jump-host` | `thor` |

---

# 🔐 Step 1 — Connect to App Server 3

From the Jump Host, connect to `stapp03`:

```bash
ssh banner@stapp03
```

Enter the password when prompted.

Verify that you are on the correct server:

```bash
hostname
```

Expected:

```text
stapp03
```

You can also run:

```bash
whoami
```

Expected:

```text
banner
```

---

# 🔎 Step 2 — Verify Whether the User Already Exists

Before creating the user, check whether `yousuf` already exists.

```bash
getent passwd yousuf
```

### If the user does not exist

There should be **no output**.

You can also run:

```bash
id yousuf
```

Expected if the user does not exist:

```text
id: ‘yousuf’: no such user
```

This confirms that the username is available.

---

# 🔎 Step 3 — Verify Whether UID `1863` Is Available

The task requires UID `1863`, so we must ensure another user isn't already using it.

Run:

```bash
getent passwd 1863
```

If there is **no output**, UID `1863` is available.

Another way to verify:

```bash
awk -F: '$3 == 1863 {print}' /etc/passwd
```

Again, no output means the UID is currently unused.

### Why is this check important?

Linux identifies users internally using their UID.

If UID `1863` already belongs to another user, assigning it to `yousuf` could create an identity conflict.

---

# 🔎 Step 4 — Check the Home Directory

The required home directory is:

```text
/var/www/yousuf
```

Check whether it already exists:

```bash
ls -ld /var/www/yousuf
```

If it doesn't exist, you may see:

```text
ls: cannot access '/var/www/yousuf': No such file or directory
```

That's okay.

We can create it automatically using the `-m` option with `useradd`.

---

# 👤 Step 5 — Create the User

Create the user with the required UID and home directory:

```bash
sudo useradd -u 1863 -d /var/www/yousuf -m yousuf
```

### Command Breakdown

```text
sudo
```

Runs the command with administrator privileges.

```text
useradd
```

Creates a new Linux user.

```text
-u 1863
```

Assigns UID `1863`.

```text
-d /var/www/yousuf
```

Sets the user's home directory.

```text
-m
```

Creates the home directory if it doesn't already exist.

```text
yousuf
```

The username being created.

---

# ✅ Step 6 — Verify the User

Check the user's UID and groups:

```bash
id yousuf
```

Expected output should contain:

```text
uid=1863(yousuf)
```

The exact GID and group information may vary depending on the system configuration.

---

# ✅ Step 7 — Verify the User Account Entry

Run:

```bash
getent passwd yousuf
```

You should see an entry similar to:

```text
yousuf:x:1863:1863::/var/www/yousuf:/bin/bash
```

The important values are:

```text
Username     → yousuf
UID          → 1863
Home         → /var/www/yousuf
```

---

# ✅ Step 8 — Verify the Home Directory

Run:

```bash
ls -ld /var/www/yousuf
```

You should see that the directory exists.

For example:

```text
drwx------ 2 yousuf yousuf ... /var/www/yousuf
```

The exact permissions can vary depending on the distribution and configuration.

---

# 🔍 Step 9 — Final Verification

Run all important checks together:

```bash
id yousuf
getent passwd yousuf
ls -ld /var/www/yousuf
```

You can also verify the UID directly:

```bash
getent passwd 1863
```

The final result should confirm:

```text
Username:       yousuf
UID:            1863
Home Directory: /var/www/yousuf
```

---

# 🧠 Important Linux Concepts

## `useradd` vs `id`

### `useradd`

Used to **create** a user:

```bash
sudo useradd ...
```

### `id`

Used to **verify** an existing user's identity:

```bash
id yousuf
```

---

## `getent passwd`

`getent passwd` queries the system's configured user database.

For a specific user:

```bash
getent passwd yousuf
```

For a specific UID:

```bash
getent passwd 1863
```

This is generally preferable to checking only `/etc/passwd`, because `getent` can also work with configured directory services such as LDAP.

---

# ⚠️ Common Mistake

Do not create the user immediately without checking whether the username and UID are already in use.

### Bad workflow

```text
Create user
    ↓
Hope username is available
    ↓
Hope UID is available
```

### Better workflow

```text
Check username
      ↓
Check UID
      ↓
Check home directory
      ↓
Create user
      ↓
Verify configuration
```

This is the safer approach for Linux system administration.

---

# 📝 Complete Command Summary

### Connect

```bash
ssh banner@stapp03
```

### Verify server

```bash
hostname
whoami
```

### Pre-check

```bash
getent passwd yousuf
getent passwd 1863
ls -ld /var/www/yousuf
```

### Create user

```bash
sudo useradd -u 1863 -d /var/www/yousuf -m yousuf
```

### Post-check

```bash
id yousuf
getent passwd yousuf
ls -ld /var/www/yousuf
```

---

# 🎯 Final Result

The custom Apache application user has been configured as required:

```text
Username       : yousuf
UID            : 1863
Home Directory : /var/www/yousuf
Server         : stapp03
```

---

# 📚 What This Task Teaches

This lab provides hands-on practice with:

- Linux user management
- `useradd`
- Linux UID management
- Home directory configuration
- `id`
- `getent`
- `/etc/passwd`
- User pre-validation
- Post-change verification
- Basic Linux security practices

These are fundamental skills for **Linux System Administrator, DevOps, Cloud Engineer, SRE, and Infrastructure Engineer** roles.