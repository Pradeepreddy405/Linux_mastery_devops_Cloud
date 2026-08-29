# 👤 Linux User Creation with a Non-Interactive Shell

## 📌 Task Overview

The system administration team at **xFusionCorp Industries** requires a user for a backup agent.

The backup agent requires a **non-interactive shell**, so the user must be created with:

```text
/sbin/nologin
```

### Task Requirement

Create a user named:

```text
jim
```

with a non-interactive shell on:

```text
App Server 2
```

---

# 🎯 Task Details

| Requirement | Value |
|---|---|
| User | `jim` |
| Target Server | `stapp02` |
| App Server | App Server 2 |
| Login User | `steve` |
| Required Shell | `/sbin/nologin` |

---

# 🏗️ Infrastructure

The lab is accessed through the **Jump Host**.

```text
Jump Host
   |
   | SSH
   v
stapp02
(App Server 2)
   |
   └── Create user: jim
       Shell: /sbin/nologin
```

---

# 🔄 Execution Approach

The task was performed using:

```text
1. Connect to Jump Host
        ↓
2. SSH to App Server 2
        ↓
3. Check whether user jim exists
        ↓
4. Check available shells
        ↓
5. Create jim with /sbin/nologin
        ↓
6. Verify jim's account entry
        ↓
7. Verify /sbin/nologin
```

The important principle is:

> **Verify → Change → Verify**

---

# 1️⃣ Connect to the Jump Host

The lab session starts on the Jump Host.

Verify the current user:

```bash
whoami
```

### Expected output

```text
thor
```

This confirms that the current session is running as the Jump Host user `thor`.

---

# 2️⃣ Connect to App Server 2

From the Jump Host, connect to App Server 2 using the provided login user:

```bash
ssh steve@stapp02
```

Enter the password when prompted.

After login, the prompt should indicate that you are on:

```text
stapp02
```

---

# 3️⃣ CHECK — Verify Whether `jim` Already Exists

Before creating the user, check whether `jim` already exists:

```bash
getent passwd jim
```

### Expected result when the user does not exist

There should be **no output**.

In the lab execution, the command returned no user entry, confirming that `jim` did not exist before the change.

### Why use `getent passwd`?

`getent passwd jim` checks the configured user/account database for the user `jim`.

It is a safe way to verify the current state before creating an account.

---

# 4️⃣ CHECK — Verify the Available Shells

The task requires a non-interactive shell.

Check the shells configured on the server:

```bash
cat /etc/shells
```

The output in the lab included:

```text
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
/usr/bin/tmux
/bin/tmux
```

The required shell was:

```text
/sbin/nologin
```

The important point from the task is that `/sbin/nologin` is the shell that must be assigned to `jim`.

---

# 5️⃣ CHANGE — Create the User with `/sbin/nologin`

Create the user and explicitly specify the required shell:

```bash
sudo useradd -s /sbin/nologin jim
```

### Command breakdown

```text
sudo
```

Runs the command with elevated privileges.

```text
useradd
```

Creates a new Linux user.

```text
-s
```

Specifies the login shell for the user.

```text
/sbin/nologin
```

Sets the user's shell to a non-interactive login shell.

```text
jim
```

The username being created.

### Complete command

```bash
sudo useradd -s /sbin/nologin jim
```

---

# 6️⃣ VERIFY — Check the User

After creating the user, verify the account:

```bash
getent passwd jim
```

### Actual result shown in the lab

```text
jim:x:1001:1001::/home/jim:/sbin/nologin
```

This confirms that:

- User `jim` exists.
- UID is `1001`.
- Primary GID is `1001`.
- Home directory is `/home/jim`.
- Login shell is `/sbin/nologin`.

> The UID/GID values are specific to the state of the lab server. The important requirement is that the user exists and its shell is `/sbin/nologin`.

---

# 7️⃣ VERIFY — Check the Shell Configuration

The lab execution also checked:

```bash
cat /etc/shells
```

The purpose of this check is to inspect the shell configuration on the server.

The required shell for the new account is:

```text
/sbin/nologin
```

The created user's `/etc/passwd` entry already confirms this:

```text
jim:x:1001:1001::/home/jim:/sbin/nologin
```

---

# 🧠 Understanding `/sbin/nologin`

A Linux user normally has a login shell associated with the account.

For example:

```text
/bin/bash
```

allows an interactive shell.

For a service or agent account that should not provide an interactive login, a non-interactive shell can be assigned:

```text
/sbin/nologin
```

Therefore:

```text
Human user
   ↓
/bin/bash
   ↓
Interactive shell


Service / Agent user
   ↓
/sbin/nologin
   ↓
Non-interactive login
```

For this task, `jim` is intended for the backup agent, so the required shell is:

```text
/sbin/nologin
```

---

# 🛠️ Important Command Explained

## `useradd -s`

The general syntax is:

```bash
useradd -s <shell> <username>
```

For this task:

```bash
sudo useradd -s /sbin/nologin jim
```

Meaning:

```text
Create user
    ↓
jim
    ↓
Set login shell
    ↓
/sbin/nologin
```

---

# 🔎 Why Did We Use `getent passwd jim`?

Command:

```bash
getent passwd jim
```

is useful for both **pre-check** and **post-check**.

### Before creation

```bash
getent passwd jim
```

No output means:

```text
jim does not exist
```

### After creation

```bash
getent passwd jim
```

Output:

```text
jim:x:1001:1001::/home/jim:/sbin/nologin
```

means:

```text
jim exists
       +
correct shell configured
```

This makes `getent` useful for validating the change.

---

# 📋 Understanding the `/etc/passwd` Entry

The resulting entry was:

```text
jim:x:1001:1001::/home/jim:/sbin/nologin
```

The fields are separated by `:`.

```text
jim
│
├── Username
│
├── x
│   └── Password placeholder
│
├── 1001
│   └── UID
│
├── 1001
│   └── Primary GID
│
├── empty
│   └── GECOS/comment field
│
├── /home/jim
│   └── Home directory
│
└── /sbin/nologin
    └── Login shell
```

The most important field for this task is the last field:

```text
/sbin/nologin
```

---

# ⚠️ Common Mistakes

## ❌ Mistake 1 — Create the user without specifying the shell

```bash
sudo useradd jim
```

This does not explicitly satisfy the task requirement.

### ✅ Correct

```bash
sudo useradd -s /sbin/nologin jim
```

---

## ❌ Mistake 2 — Work on the wrong server

The task specifically requires **App Server 2**.

Always connect using:

```bash
ssh steve@stapp02
```

---

## ❌ Mistake 3 — Skip the pre-check

Do not immediately run:

```bash
sudo useradd jim
```

First check:

```bash
getent passwd jim
```

If the user already exists, investigate its current configuration instead of blindly creating it.

---

# 🧪 Exact Command Sequence Used

The command flow shown in the lab was:

### On Jump Host

```bash
whoami
```

Expected:

```text
thor
```

### Connect to App Server 2

```bash
ssh steve@stapp02
```

### Check whether `jim` exists

```bash
getent passwd jim
```

### Check available shells

```bash
cat /etc/shells
```

### Create the user

```bash
sudo useradd -s /sbin/nologin jim
```

### Verify the user

```bash
getent passwd jim
```

Expected:

```text
jim:x:1001:1001::/home/jim:/sbin/nologin
```

### Check shell configuration again

```bash
cat /etc/shells
```

---

# ✅ Final Verification

The key final verification is:

```bash
getent passwd jim
```

Expected:

```text
jim:x:1001:1001::/home/jim:/sbin/nologin
```

The final state is:

```text
Server: stapp02
       │
       └── User: jim
              │
              └── Shell: /sbin/nologin
```

---

# 📊 Final State

| Item | Expected State |
|---|---|
| Target Server | `stapp02` |
| User | `jim` |
| User Exists | ✅ |
| Home Directory | `/home/jim` |
| Shell | `/sbin/nologin` |
| Interactive Shell | ❌ |
| Task Status | ✅ Completed |

---

# 🧠 Interview Explanation

### Q: How did you complete this task?

**Answer:**

> I first connected to App Server 2 through the Jump Host using `ssh steve@stapp02`. I verified whether the `jim` user already existed using `getent passwd jim`. Since there was no output, I confirmed the required shell configuration and created the user using `sudo useradd -s /sbin/nologin jim`. Finally, I verified the user using `getent passwd jim` and confirmed that `/sbin/nologin` was assigned as the login shell.

---

### Q: Why did you use `/sbin/nologin`?

**Answer:**

> The account is intended for a backup agent rather than interactive human use. `/sbin/nologin` provides a non-interactive shell, preventing the account from being used as a normal interactive login account.

---

### Q: What does `-s` do in `useradd`?

**Answer:**

> The `-s` option specifies the user's login shell. In this task, I used `-s /sbin/nologin` so that `jim` receives a non-interactive shell.

---

### Q: How did you verify that the correct shell was configured?

**Answer:**

> I used `getent passwd jim`. The final field of the returned `/etc/passwd` entry was `/sbin/nologin`, confirming the required shell.

---

# 🎯 Final Result

The task was successfully completed on **App Server 2 (`stapp02`)**.

```text
stapp02
   │
   └── jim
       │
       └── /sbin/nologin
```

### Status

```text
Target Server: stapp02       ✅
User: jim                    ✅
Non-interactive shell        ✅
Shell: /sbin/nologin         ✅
Task completed               ✅
```

---

# 📚 Quick Reference

```bash
# Connect to App Server 2
ssh steve@stapp02

# Check user
getent passwd jim

# Check shells
cat /etc/shells

# Create user with non-interactive shell
sudo useradd -s /sbin/nologin jim

# Verify user and shell
getent passwd jim
```

**Core concept:**

```text
useradd
   +
-s /sbin/nologin
   ↓
Create a user with a non-interactive shell
```
