# 👤 Create a Linux User Without a Home Directory

## 📌 Task Overview

As part of the **xFusionCorp Industries / Nautilus infrastructure**, the system administration team required the creation of a service user account on **Application Server 2**.

### Requirement

Create a Linux user named:

```text
kirsty
```

on:

```text
Application Server 2
Hostname: stapp02
```

The user must be created **without a home directory**.

---

## 🖥️ Infrastructure Details

| Server | Hostname | Login User | Purpose |
|---|---|---|---|
| Application Server 2 | `stapp02` | `steve` | Target server |

---

## 🎯 Objective

- Connect to **Application Server 2 (`stapp02`)**
- Create the user `kirsty`
- Prevent creation of `/home/kirsty`
- Verify that the user exists
- Verify that the home directory does not exist

---

# 🔧 Step-by-Step Implementation

## 1. Connect to Application Server 2

From the jump host, connect to `stapp02` using the provided user:

```bash
ssh steve@stapp02
```

Verify the current user:

```bash
whoami
```

Expected:

```text
steve
```

Verify the hostname:

```bash
hostname
```

Expected:

```text
stapp02
```

---

## 2. Check Whether the User Already Exists

Before making any changes, check whether `kirsty` already exists:

```bash
id kirsty
```

In the task screenshot, the user did not exist:

```text
id: ‘kirsty’: no such user
```

Also check whether the expected home directory already exists:

```bash
ls -ld /home/kirsty
```

Expected:

```text
ls: cannot access '/home/kirsty': No such file or directory
```

This follows the standard **check → change → verify** workflow.

---

## 3. Create the User Without a Home Directory

Create the user using `useradd` with the `-M` option:

```bash
sudo useradd -M kirsty
```

### What does `-M` mean?

The `-M` option tells `useradd`:

> **Do not create the user's home directory.**

For example:

```bash
sudo useradd kirsty
```

may create:

```text
/home/kirsty
```

But:

```bash
sudo useradd -M kirsty
```

creates the user account **without creating the directory**.

### Important

The `-M` option prevents creation of the directory. It does **not** remove a home directory if one already exists.

---

# 🔍 Verification

## 4. Verify That the User Exists

Run:

```bash
id kirsty
```

Example output:

```text
uid=1001(kirsty) gid=1001(kirsty) groups=1001(kirsty)
```

> The UID/GID values can be different depending on the server.

The task screenshot showed:

```text
uid=1001(kirsty) gid=1001(kirsty) groups=1001(kirsty)
```

This confirms that the Linux user account was successfully created.

---

## 5. Check the User's Account Configuration

Use:

```bash
getent passwd kirsty
```

The screenshot showed:

```text
kirsty:x:1001:1001::/home/kirsty:/bin/bash
```

The structure of `/etc/passwd` is:

```text
username:x:UID:GID:GECOS:home_directory:shell
```

So:

```text
kirsty:x:1001:1001::/home/kirsty:/bin/bash
```

means:

| Field | Value | Meaning |
|---|---|---|
| Username | `kirsty` | User account name |
| Password field | `x` | Password information is stored separately |
| UID | `1001` | User ID |
| GID | `1001` | Primary group ID |
| GECOS | empty | Comment/information field |
| Home | `/home/kirsty` | Configured home path |
| Shell | `/bin/bash` | Login shell |

### ⚠️ Important Concept

Seeing:

```text
/home/kirsty
```

in `getent passwd kirsty` **does not mean that the directory exists**.

It is only the **configured home-directory path** stored in the account record.

The actual directory can still be absent because we used:

```bash
useradd -M kirsty
```

---

## 6. Verify That the Home Directory Was Not Created

Run:

```bash
ls -ld /home/kirsty
```

Expected:

```text
ls: cannot access '/home/kirsty': No such file or directory
```

This is the key verification for the task.

You can also use:

```bash
test ! -d /home/kirsty && echo "PASS: Home directory does not exist"
```

Expected:

```text
PASS: Home directory does not exist
```

---

# ✅ Final Validation

Run all important checks together:

```bash
id kirsty && getent passwd kirsty && test ! -d /home/kirsty && echo "PASS: kirsty created without home directory"
```

Expected:

```text
uid=1001(kirsty) gid=1001(kirsty) groups=1001(kirsty)
kirsty:x:1001:1001::/home/kirsty:/bin/bash
PASS: kirsty created without home directory
```

---

# 🧠 Key Linux Concepts

## `useradd`

Creates a new Linux user account.

```bash
sudo useradd username
```

---

## `useradd -M`

Creates the user **without creating a home directory**.

```bash
sudo useradd -M username
```

For this task:

```bash
sudo useradd -M kirsty
```

---

## `id`

Displays the UID, GID, and group membership of a user.

```bash
id kirsty
```

Example:

```text
uid=1001(kirsty) gid=1001(kirsty) groups=1001(kirsty)
```

---

## `getent passwd`

Retrieves the user's account information from the configured system identity database.

```bash
getent passwd kirsty
```

It is useful for checking the user's:

- Username
- UID
- GID
- Home-directory configuration
- Login shell

---

## `/etc/passwd`

Linux stores basic user-account information in:

```text
/etc/passwd
```

A typical entry looks like:

```text
username:x:UID:GID:GECOS:home:shell
```

Example:

```text
kirsty:x:1001:1001::/home/kirsty:/bin/bash
```

---

# 🔄 Check → Change → Verify Workflow

A useful Linux administration pattern demonstrated by this task is:

```text
        CHECK
          │
          ▼
   Does kirsty exist?
          │
          ▼
    Does /home/kirsty
        exist?
          │
          ▼
        CHANGE
          │
          ▼
 sudo useradd -M kirsty
          │
          ▼
       VERIFY
          │
          ▼
    id kirsty
          │
          ▼
 getent passwd kirsty
          │
          ▼
 /home/kirsty does NOT exist
```

This approach helps avoid unnecessary or incorrect changes.

---

# 📸 Task Execution Evidence

The provided task screenshot shows the complete execution flow:

1. SSH from the jump host to `stapp02`
2. Verify the target hostname
3. Confirm `kirsty` does not initially exist
4. Confirm `/home/kirsty` does not exist
5. Run:

```bash
sudo useradd -M kirsty
```

6. Verify the account using:

```bash
id kirsty
```

7. Inspect the account using:

```bash
getent passwd kirsty
```

8. Confirm `/home/kirsty` was not created

---

# 🏆 Final Result

| Requirement | Status |
|---|---|
| User `kirsty` created | ✅ |
| Target server `stapp02` | ✅ |
| User created without home directory | ✅ |
| `/home/kirsty` does not exist | ✅ |
| User verified with `id` | ✅ |
| Account configuration verified | ✅ |

---

# 💡 What I Learned

- How to connect to a specific application server using SSH.
- How to check whether a Linux user already exists.
- How to create a Linux user with `useradd`.
- How the `-M` option prevents home-directory creation.
- How to verify users using `id`.
- How to inspect account records using `getent passwd`.
- The difference between a **configured home-directory path** and an **actual directory on disk**.
- How to follow a safe **check → change → verify** workflow during Linux administration.
