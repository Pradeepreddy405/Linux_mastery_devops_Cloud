# Grant Executable Permissions to `xfusioncorp.sh`

## Task Description

The `xFusionCorp Industries` sysadmin team has distributed a Bash script named `xfusioncorp.sh` to the required servers.

The script on **App Server 2** does not have executable permissions.

The task is to:

1. Grant executable permission to `/tmp/xfusioncorp.sh`.
2. Ensure **all users** can execute the script.
3. Verify the updated permissions.

---

## Server Details

| Server | Hostname | User |
|---|---|---|
| App Server 2 | `stapp02` | `steve` |

---

##  Solution

### Step 1: Connect to App Server 2

From the jump host:

```bash
ssh steve@stapp02
```

Enter the password provided by the KodeKloud infrastructure details.

---

### Step 2: Check Existing Permissions

```bash
ls -l /tmp/xfusioncorp.sh
```

This displays the current permissions of the script.

Example:

```text
---------- 1 root root 120 Sep 1 14:40 /tmp/xfusioncorp.sh
```

The file does not have the required execute permissions.

---

### Step 3: Grant Read and Execute Permissions to Everyone

Run:

```bash
sudo chmod a+rx /tmp/xfusioncorp.sh
```

### Understanding the Command

```text
chmod a+rx /tmp/xfusioncorp.sh
     │ │
     │ └── Add read (r) and execute (x)
     │
     └──── All users
```

`a` means **all users**:

- Owner
- Group
- Others

Therefore:

```bash
chmod a+rx /tmp/xfusioncorp.sh
```

gives everyone read and execute permissions.

---

## Step 4: Verify Permissions

Run:

```bash
ls -l /tmp/xfusioncorp.sh
```

Expected output:

```text
-r-xr-xr-x 1 root root 120 Sep 1 14:40 /tmp/xfusioncorp.sh
```

The important part is:

```text
-r-xr-xr-x
```

This means:

| Category | Permission |
|---|---|
| Owner | `r-x` |
| Group | `r-x` |
| Others | `r-x` |

So all users can read and execute the script.

---

##  Optional Verification Using `stat`

You can also run:

```bash
stat /tmp/xfusioncorp.sh
```

Look for:

```text
Access: (0555/-r-xr-xr-x)
```

`stat` provides detailed file metadata, including permissions, ownership, timestamps, size, and inode information.

---

##  Linux Permission Breakdown

The permission:

```text
-r-xr-xr-x
```

can be understood as:

```text
- r-x r-x r-x
│ │   │   │
│ │   │   └── Others
│ │   └────── Group
│ └────────── Owner
└──────────── File type
```

Numeric representation:

```text
555
```

Because:

```text
r = 4
w = 2
x = 1
```

Therefore:

```text
r-x = 4 + 1 = 5
```

For all three categories:

```text
5 5 5
```

So:

```text
-r-xr-xr-x
```

is equivalent to:

```text
0555
```

---

## Why Not Use `777`?

You could technically run:

```bash
sudo chmod 777 /tmp/xfusioncorp.sh
```

but this is **bad practice**.

`777` gives everyone:

```text
read + write + execute
```

That means any user could modify the script.

For this requirement, users only need to **read and execute** it.

Therefore:

```bash
sudo chmod a+rx /tmp/xfusioncorp.sh
```

is the safer choice.

---

##  Commands Summary

```bash
# Connect to App Server 2
ssh steve@stapp02

# Check current permissions
ls -l /tmp/xfusioncorp.sh

# Grant read + execute permissions to everyone
sudo chmod a+rx /tmp/xfusioncorp.sh

# Verify
ls -l /tmp/xfusioncorp.sh

# Detailed verification
stat /tmp/xfusioncorp.sh
```

---

##  Expected Final State

The script should have:

```text
-r-xr-xr-x
```

or numerically:

```text
0555
```

This ensures **all users can execute `/tmp/xfusioncorp.sh` without giving them permission to modify the script**.

---

##  Key Concepts Learned

- Linux file permissions
- `chmod`
- Read (`r`) permission
- Execute (`x`) permission
- Owner / Group / Others
- Numeric permissions (`555`)
- Symbolic permissions (`a+rx`)
- `ls -l`
- `stat`
- Principle of least privilege
- Secure Linux file permission management