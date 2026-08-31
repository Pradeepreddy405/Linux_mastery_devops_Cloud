# Create a Temporary User with Expiry Date – Nautilus Project

## Lab Overview

As part of the **Nautilus project**, a developer named `yousuf` required temporary access to **Application Server 3** in the **Stratos Datacenter**.

The requirement was to:

- Create a Linux user named `yousuf`
- Create the user on **App Server 3 (`stapp03`)**
- Ensure the username is lowercase
- Configure the account expiry date as **2027-02-17**
- Verify that the user was created successfully
- Verify that the expiry date was configured correctly

---

## Infrastructure

| Server | Hostname | User | Purpose |
|---|---|---|---|
| Application Server 3 | `stapp03` | `banner` | Nautilus Application 3 |
| Jump Host | `jump-host` | `thor` | Secure access to Stratos DC |

---

## Task Requirements

Create the following user:

```text
Username: yousuf
Server: stapp03
Expiry Date: 2027-02-17
```

---

# Implementation

## Step 1: Connect to App Server 3

From the Jump Host:

```bash
ssh banner@stapp03
```

Verify the server and current user:

```bash
hostname
whoami
```

Expected:

```text
stapp03
banner
```

---

## Step 2: Check Whether the User Already Exists

Before making any changes, verify the existing user state:

```bash
id yousuf
```

If the user does not exist, the command should return something similar to:

```text
id: ‘yousuf’: no such user
```

This prevents accidentally modifying or recreating an existing account.

---

## Step 3: Create the User with an Expiry Date

Create the user using `useradd`:

```bash
sudo useradd -e 2027-02-17 yousuf
```

The `-e` option sets the account expiration date.

### Command breakdown

```text
sudo
```

Runs the command with administrative privileges.

```text
useradd
```

Creates a new Linux user.

```text
-e 2027-02-17
```

Sets the account expiration date.

```text
yousuf
```

Specifies the username.

---

## Step 4: Verify the User

Check that the user exists:

```bash
id yousuf
```

Example:

```text
uid=xxxx(yousuf) gid=xxxx(yousuf) groups=xxxx(yousuf)
```

---

## Step 5: Verify the Expiry Date

Use `chage` to inspect the account aging information:

```bash
sudo chage -l yousuf
```

Look for:

```text
Account expires                                    : Feb 17, 2027
```

You can also filter the output:

```bash
sudo chage -l yousuf | grep "Account expires"
```

Expected:

```text
Account expires                                    : Feb 17, 2027
```

---

# Verification Checklist

| Check | Command | Expected Result |
|---|---|---|
| Correct server | `hostname` | `stapp03` |
| Correct login user | `whoami` | `banner` |
| User exists | `id yousuf` | User information displayed |
| Username | `getent passwd yousuf` | `yousuf` entry exists |
| Expiry date | `sudo chage -l yousuf` | `Feb 17, 2027` |

---

# Important Concepts

## User Account Expiration vs Password Expiration

These are different concepts.

### Account expiration

```bash
useradd -e 2027-02-17 yousuf
```

This determines **when the user account itself becomes expired**.

### Password expiration

Password aging can be managed using:

```bash
chage
```

For example:

```bash
chage -M 90 yousuf
```

This controls password lifetime and is separate from the account expiration date.

---

#  Useful Commands

### Check user information

```bash
id yousuf
```

### Check whether the user exists

```bash
getent passwd yousuf
```

### Display account aging information

```bash
sudo chage -l yousuf
```

### Check the account expiration field directly

```bash
sudo chage -l yousuf | grep "Account expires"
```

### Check the `/etc/shadow` entry

```bash
sudo grep '^yousuf:' /etc/shadow
```

---

# Operational Workflow

This lab follows a basic production-style Linux administration workflow:

```text
        ┌──────────────┐
        │   CHECK      │
        │              │
        │ hostname     │
        │ whoami       │
        │ id yousuf    │
        └──────┬───────┘
               │
               ▼
        ┌──────────────┐
        │    CHANGE    │
        │              │
        │ useradd -e   │
        │ 2027-02-17   │
        └──────┬───────┘
               │
               ▼
        ┌──────────────┐
        │   VERIFY     │
        │              │
        │ id yousuf    │
        │ chage -l     │
        └──────────────┘
```

---

# Final Result

The temporary developer account was successfully configured as:

```text
Username: yousuf
Server:   stapp03
Expiry:   2027-02-17
```

The account is therefore configured for **time-limited access**, satisfying the Nautilus project requirement.

---

## Key Takeaways

- Always verify the target server before making administrative changes.
- Check whether a user already exists before creating it.
- Use `useradd -e` to create an account with an expiration date.
- Use `id` and `getent passwd` to verify user creation.
- Use `chage -l` to verify account aging and expiration settings.
- Separate **account expiration** from **password expiration**.
- Follow the **Check → Change → Verify** workflow for Linux administration.