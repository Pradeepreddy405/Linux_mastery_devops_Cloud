# Copy Confidential File to App Server 3

##  Task Description

A `Nautilus` developer has stored confidential data on the jump host within the `Stratos DC`.

The system administration team needs to securely transfer the encrypted file:

```text
/tmp/nautilus.txt.gpg
```

from the **Jump Host** to **Application Server 3** and place it in:

```text
/home/data
```

The file transfer is performed using `scp`, which securely copies files between hosts over an SSH connection.

---

## Infrastructure

| Server | Hostname | User | Purpose |
|---|---|---|---|
| Application Server 3 | `stapp03` | `banner` | Hosts Nautilus Application 3 |
| Jump Host | `jump-host` | `thor` | Secure access to Stratos DC |

### Application Server 3 Credentials

```text
Username: banner
Password: BigGr33n
Hostname: stapp03
```

### Jump Host

```text
Username: thor
Hostname: jump-host
```

---

## Objective

Copy:

```text
/tmp/nautilus.txt.gpg
```

from:

```text
jump-host
```

to:

```text
stapp03:/home/data/
```

---

## Step-by-Step Solution

### 1. Verify the source file

On the jump host:

```bash
ls -l /tmp/nautilus.txt.gpg
```

Expected result should show that the file exists.

---

### 2. Verify the destination directory

Test whether `/home/data` exists on App Server 3:

```bash
ssh banner@stapp03 "ls -ld /home/data"
```

If it does not exist, create it:

```bash
ssh banner@stapp03 "sudo mkdir -p /home/data"
```

---

### 3. Copy the file using SCP

From the **jump host**, run:

```bash
scp /tmp/nautilus.txt.gpg banner@stapp03:/home/data/
```

Enter the password when prompted:

```text
BigGr33n
```

The command follows this structure:

```text
scp SOURCE USER@REMOTE_HOST:DESTINATION
```

For this task:

```text
SOURCE
/tmp/nautilus.txt.gpg

USER
banner

REMOTE HOST
stapp03

DESTINATION
/home/data/
```

---

##  Verify the File

You do **not** need to log into App Server 3 interactively.

From the jump host, run:

```bash
ssh banner@stapp03 "ls -l /home/data/nautilus.txt.gpg"
```

If the file was copied successfully, it should return something similar to:

```text
-rw-r--r-- 1 banner banner 1234 Sep  1 15:35 /home/data/nautilus.txt.gpg
```

---

##  Verify Using `test`

A cleaner verification is:

```bash
ssh banner@stapp03 "test -f /home/data/nautilus.txt.gpg && echo 'File exists' || echo 'File not found'"
```

Expected output:

```text
File exists
```

---

##  Verify File Integrity

For stronger verification, calculate an MD5 checksum on the source:

```bash
md5sum /tmp/nautilus.txt.gpg
```

Then calculate the checksum of the destination file remotely:

```bash
ssh banner@stapp03 "md5sum /home/data/nautilus.txt.gpg"
```

The two checksums should be identical.

Example:

```text
Source:
a1b2c3d4e5f6...  /tmp/nautilus.txt.gpg

Destination:
a1b2c3d4e5f6...  /home/data/nautilus.txt.gpg
```

Identical checksums confirm that the file contents are the same.

---

##  Important Concepts

### What is `scp`?

`scp` stands for **Secure Copy**.

It is used to copy files between Linux systems over an SSH connection. Modern OpenSSH `scp` uses SFTP over SSH for transfers by default.

### Why use `scp` here?

The file needs to move between two different servers:

```text
Jump Host
    |
    | SSH / SCP
    ↓
App Server 3
```

A normal `cp` command cannot directly copy a file between separate servers.

For example:

```bash
cp /tmp/nautilus.txt.gpg /home/data/
```

only copies files **within the same server**.

Instead, use:

```bash
scp /tmp/nautilus.txt.gpg banner@stapp03:/home/data/
```

---

##  Final Command

The main command required to complete the task is:

```bash
scp /tmp/nautilus.txt.gpg banner@stapp03:/home/data/
```

Then verify:

```bash
ssh banner@stapp03 "ls -l /home/data/nautilus.txt.gpg"
```

---

##  Workflow Summary

```text
┌─────────────────────┐
│     Jump Host       │
│                     │
│ /tmp/               │
│ nautilus.txt.gpg    │
└──────────┬──────────┘
           │
           │ scp
           │ SSH
           ▼
┌─────────────────────┐
│  Application        │
│  Server 3            │
│  stapp03             │
│                     │
│ /home/data/          │
│ nautilus.txt.gpg    │
└─────────────────────┘
```

---

##  Completion Checklist

- [x] Confirm `/tmp/nautilus.txt.gpg` exists on the jump host.
- [x] Ensure `/home/data` exists on `stapp03`.
- [x] Copy the file using `scp`.
- [x] Verify the file exists on `stapp03`.
- [x] Optionally compare checksums to verify file integrity.

##  References

- OpenSSH `scp` manual: https://man7.org/linux/man-pages/man1/scp.1.html
- OpenSSH source documentation: https://github.com/openssh/openssh-portable/blob/master/scp.1