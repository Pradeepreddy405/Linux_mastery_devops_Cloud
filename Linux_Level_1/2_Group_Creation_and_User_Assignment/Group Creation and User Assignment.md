# 👥 Group Creation and User Assignment Across App Servers

## 📌 Task Overview

The `xFusionCorp Industries` system administration team is implementing **group-based access control** across the application servers in the **Stratos Datacenter**.

### Requirements

1. Create a group named `nautilus_admin_users` on **all App Servers**.
2. Ensure a user named `kano` exists on **all App Servers**.
3. Add `kano` to the `nautilus_admin_users` group on **all App Servers**.
4. Verify the configuration after making the changes.

---

# 🎯 Infrastructure

| Server | Hostname | Login User | Purpose |
|---|---|---|---|
| Application Server 1 | `stapp01` | `tony` | Application Server |
| Application Server 2 | `stapp02` | `steve` | Application Server |
| Application Server 3 | `stapp03` | `banner` | Application Server |
| Jump Host | `jump-host` | `thor` | Access to Stratos Datacenter |

### Required Configuration

| Item | Value |
|---|---|
| Group | `nautilus_admin_users` |
| User | `kano` |
| App Servers | `stapp01`, `stapp02`, `stapp03` |

---

# 🧠 Administration Approach

The task was performed using the standard Linux administration workflow:

```text
        ┌──────────────────────┐
        │  VERIFY CURRENT      │
        │       STATE          │
        └──────────┬───────────┘
                   ↓
        ┌──────────────────────┐
        │   CREATE / MODIFY    │
        │   ONLY IF REQUIRED   │
        └──────────┬───────────┘
                   ↓
        ┌──────────────────────┐
        │   VERIFY FINAL       │
        │       STATE          │
        └──────────────────────┘
```

This avoids blindly creating users or groups that may already exist.

---

# 🔎 Step 1 — Connect to App Server 1

From the Jump Host:

```bash
ssh tony@stapp01
```

Verify the server and login user:

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

## Check Group

```bash
getent group nautilus_admin_users
```

In the execution shown, the group did not exist initially, so it was created:

```bash
sudo groupadd nautilus_admin_users
```

Verify:

```bash
getent group nautilus_admin_users
```

Example result:

```text
nautilus_admin_users:x:1001:
```

> The actual GID can differ between servers unless the task explicitly requires a fixed GID.

---

## Check User

```bash
getent passwd kano
id kano
```

The initial verification showed:

```text
id: 'kano': no such user
```

Therefore, the user was created:

```bash
sudo useradd kano
```

Verify:

```bash
id kano
```

Example:

```text
uid=1001(kano) gid=1002(kano) groups=1002(kano)
```

---

## Add User to Group

```bash
sudo usermod -aG nautilus_admin_users kano
```

Verify:

```bash
id kano
```

The final output showed `kano` as a member of both the user's primary group and `nautilus_admin_users`:

```text
uid=1001(kano) gid=1002(kano) groups=1002(kano),1001(nautilus_admin_users)
```

Direct group verification:

```bash
getent group nautilus_admin_users
```

Example:

```text
nautilus_admin_users:x:1001:kano
```

---

# 🔎 Step 2 — Configure App Server 2

Connect:

```bash
ssh steve@stapp02
```

Verify:

```bash
hostname
whoami
```

Expected:

```text
stapp02
steve
```

### Check Existing Configuration

```bash
getent group nautilus_admin_users
getent passwd kano
id kano
```

The execution showed that the group and user were not present initially.

### Create Group

```bash
sudo groupadd nautilus_admin_users
```

Verify:

```bash
getent group nautilus_admin_users
```

### Create User

```bash
sudo useradd kano
```

Verify:

```bash
id kano
```

### Add User to Group

```bash
sudo usermod -aG nautilus_admin_users kano
```

Verify:

```bash
id kano
```

Observed final membership:

```text
uid=1001(kano) gid=1002(kano) groups=1002(kano),1001(nautilus_admin_users)
```

Verify the group directly:

```bash
getent group nautilus_admin_users
```

Example:

```text
nautilus_admin_users:x:1001:kano
```

---

# 🔎 Step 3 — Configure App Server 3

Connect:

```bash
ssh banner@stapp03
```

Verify:

```bash
hostname
whoami
```

Expected:

```text
stapp03
banner
```

### Check Existing Configuration

```bash
getent group nautilus_admin_users
getent passwd kano
id kano
```

The execution showed that the required group and user were not present initially.

### Create Group

```bash
sudo groupadd nautilus_admin_users
```

Verify:

```bash
getent group nautilus_admin_users
```

### Create User

```bash
sudo useradd kano
```

Verify:

```bash
id kano
```

### Add User to Group

```bash
sudo usermod -aG nautilus_admin_users kano
```

Verify:

```bash
id kano
```

Observed final membership:

```text
uid=1001(kano) gid=1002(kano) groups=1002(kano),1001(nautilus_admin_users)
```

Verify the group directly:

```bash
getent group nautilus_admin_users
```

Example:

```text
nautilus_admin_users:x:1001:kano
```

---

# 🛠️ Command Explanation

## `getent group`

```bash
getent group nautilus_admin_users
```

Checks whether the group exists and displays its group database entry.

Example:

```text
nautilus_admin_users:x:1001:kano
```

The fields are:

```text
group_name : password_placeholder : GID : members
```

---

## `getent passwd`

```bash
getent passwd kano
```

Checks whether the `kano` user exists.

Example:

```text
kano:x:1001:1002::/home/kano:/bin/bash
```

---

## `id`

```bash
id kano
```

Displays the user's:

- UID
- Primary GID
- Supplementary groups

Example:

```text
uid=1001(kano) gid=1002(kano) groups=1002(kano),1001(nautilus_admin_users)
```

This is one of the most useful commands for verifying group membership.

---

## `groupadd`

```bash
sudo groupadd nautilus_admin_users
```

Creates a new Linux group.

---

## `useradd`

```bash
sudo useradd kano
```

Creates the `kano` Linux user.

---

## `usermod -aG`

```bash
sudo usermod -aG nautilus_admin_users kano
```

Adds `kano` to the supplementary group `nautilus_admin_users`.

### Meaning of the options

```text
usermod  → Modify an existing user
-a       → Append
-G       → Supplementary groups
```

Therefore:

```bash
-aG
```

means:

> **Append the user to the specified supplementary group without removing existing supplementary groups.**

---

# ⚠️ Important: Why `-aG` Matters

Avoid using:

```bash
sudo usermod -G nautilus_admin_users kano
```

unless you intentionally want to replace the user's supplementary-group list.

Prefer:

```bash
sudo usermod -aG nautilus_admin_users kano
```

because `-a` appends the group and preserves existing supplementary group memberships.

---

# 🔍 Verification Commands

After configuration, the following commands can be used on every App Server:

```bash
hostname
getent group nautilus_admin_users
getent passwd kano
id kano
groups kano
```

### What each command verifies

| Command | Purpose |
|---|---|
| `hostname` | Confirms the current server |
| `getent group nautilus_admin_users` | Confirms the group exists |
| `getent passwd kano` | Confirms the user exists |
| `id kano` | Shows UID, GID and group memberships |
| `groups kano` | Shows groups associated with `kano` |

---

# 📊 Final Configuration

| Server | Group Exists | User Exists | `kano` in Group |
|---|---:|---:|---:|
| `stapp01` | ✅ | ✅ | ✅ |
| `stapp02` | ✅ | ✅ | ✅ |
| `stapp03` | ✅ | ✅ | ✅ |

Final logical structure:

```text
                 nautilus_admin_users
                          │
                         kano
                          │
          ┌───────────────┼───────────────┐
          │               │               │
       stapp01         stapp02         stapp03
       App Server      App Server      App Server
```

---

# 🧪 Final Validation Checklist

## App Server 1

```bash
ssh tony@stapp01

hostname
getent group nautilus_admin_users
getent passwd kano
id kano
```

Expected:

```text
stapp01
nautilus_admin_users:...
kano:...
...nautilus_admin_users...
```

---

## App Server 2

```bash
ssh steve@stapp02

hostname
getent group nautilus_admin_users
getent passwd kano
id kano
```

Expected:

```text
stapp02
nautilus_admin_users:...
kano:...
...nautilus_admin_users...
```

---

## App Server 3

```bash
ssh banner@stapp03

hostname
getent group nautilus_admin_users
getent passwd kano
id kano
```

Expected:

```text
stapp03
nautilus_admin_users:...
kano:...
...nautilus_admin_users...
```

---

# 💡 Important Linux Concepts Learned

This task provides hands-on practice with:

- Linux users
- Linux groups
- Primary groups
- Supplementary groups
- Group-based access control
- `groupadd`
- `useradd`
- `usermod`
- `id`
- `groups`
- `getent`
- Pre-change validation
- Post-change verification
- Multi-server configuration

---

# 🧠 Interview Explanation

### Question: How did you complete this task?

**Answer:**

> I first connected to each application server through the Jump Host. Before making any changes, I verified whether the `nautilus_admin_users` group and `kano` user already existed using `getent` and `id`.
>
> Where they were missing, I created the group with `groupadd` and the user with `useradd`. I then added `kano` to the group using `usermod -aG`.
>
> Finally, I verified the configuration using `getent group`, `getent passwd`, `id`, and `groups` on all three application servers.

### Question: Why did you use `usermod -aG` instead of `usermod -G`?

**Answer:**

> `-G` specifies supplementary groups, but without `-a`, the existing supplementary-group list can be replaced. `-aG` appends the new group while preserving the user's existing supplementary group memberships.

### Question: Why did you verify before making changes?

**Answer:**

> Pre-change verification prevents unnecessary changes and avoids errors such as attempting to create a group or user that already exists. It also follows the standard infrastructure practice of checking the current state before changing it.

---

# 🚀 Production Perspective

The task was completed manually because it involved only three servers.

In a real DevOps/SRE environment, manually applying the same configuration to many servers does not scale.

A configuration-management tool such as **Ansible** could define the desired state:

```text
              Ansible
                 │
       ┌─────────┼─────────┐
       ↓         ↓         ↓
    stapp01   stapp02   stapp03
       │         │         │
       ↓         ↓         ↓
 Create group  Create group  Create group
       │         │         │
       ↓         ↓         ↓
 Ensure kano  Ensure kano  Ensure kano
       │         │         │
       ↓         ↓         ↓
 Add to group Add to group Add to group
```

The desired state would be:

```text
Group exists
      +
User exists
      +
User belongs to group
```

This task therefore provides a practical foundation for **Linux administration, access control, and configuration management**.

---

# 📝 Complete Command Reference

### Check group

```bash
getent group nautilus_admin_users
```

### Check user

```bash
getent passwd kano
```

### Check user and groups

```bash
id kano
```

### Check groups

```bash
groups kano
```

### Create group

```bash
sudo groupadd nautilus_admin_users
```

### Create user

```bash
sudo useradd kano
```

### Add user to group

```bash
sudo usermod -aG nautilus_admin_users kano
```

### Final verification

```bash
hostname
getent group nautilus_admin_users
getent passwd kano
id kano
groups kano
```

---

# 🎯 Final Result

The task was successfully completed across all three application servers.

```text
stapp01
 └── nautilus_admin_users
       └── kano

stapp02
 └── nautilus_admin_users
       └── kano

stapp03
 └── nautilus_admin_users
       └── kano
```

### Status

```text
Group: nautilus_admin_users     ✅
User:  kano                     ✅
stapp01 membership              ✅
stapp02 membership              ✅
stapp03 membership              ✅
```

**Result: Successfully completed the KodeKloud task.**
