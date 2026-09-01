# Configure ACL Permissions on `/etc/hosts`

##  Task Description

The Nautilus security team identified incorrect permissions on the `/etc/hosts` file on **Nautilus Application Server 3 (`stapp03`)**.

The task is to correct the file ownership, standard permissions, and user-specific Access Control Lists (ACLs).

### Requirements

The `/etc/hosts` file must satisfy the following:

1. User owner must be `root`.
2. Group owner must be `root`.
3. `Others` must have **read-only** permission.
4. User `anita` must have **no permissions**.
5. User `jerome` must have **read-only** permission.

---

##  Infrastructure

| Server | Hostname | User | Purpose |
|---|---|---|---|
| Application Server 3 | `stapp03` | `banner` | Nautilus Application 3 |

---

#  Solution

## Step 1: Connect to App Server 3

From the jump host:

```bash
ssh banner@stapp03
```

Then switch to root:

```bash
sudo -i
```

---

## Step 2: Check Existing Permissions

Before making changes, inspect the current permissions:

```bash
ls -l /etc/hosts
```

Check the existing ACL configuration:

```bash
getfacl /etc/hosts
```

`getfacl` displays the file owner, group owner, base permissions, named user/group ACL entries, and the effective rights mask.

---

## Step 3: Change Owner and Group

Set both the user owner and group owner to `root`:

```bash
chown root:root /etc/hosts
```

Verify:

```bash
ls -l /etc/hosts
```

Expected ownership:

```text
root root
```

---

## Step 4: Set Standard File Permissions

Set the file permissions to `644`:

```bash
chmod 644 /etc/hosts
```

This results in:

```text
rw-r--r--
```

Meaning:

| Entity | Permission |
|---|---|
| Owner (`root`) | Read + Write |
| Group (`root`) | Read |
| Others | Read |

Therefore, `Others` have the required **read-only** access.

---

#  Step 5: Configure ACL for `anita`

The requirement says that `anita` must have **no permissions**.

Run:

```bash
setfacl -m u:anita:--- /etc/hosts
```

Explanation:

```text
setfacl       → Modify file ACL
-m            → Modify an ACL entry
u:anita       → User named anita
---           → No read, write, or execute permissions
```

`setfacl -m` is specifically used to modify ACL entries.

---

#  Step 6: Configure ACL for `jerome`

The requirement says that `jerome` must have **read-only** access.

Run:

```bash
setfacl -m u:jerome:r-- /etc/hosts
```

Explanation:

```text
setfacl       → Modify file ACL
-m            → Modify an ACL entry
u:jerome      → User named jerome
r--           → Read-only
```

---

#  Step 7: Verify the ACL

Run:

```bash
getfacl /etc/hosts
```

The output should contain entries similar to:

```text
# file: /etc/hosts
# owner: root
# group: root

user::rw-
user:anita:---
user:jerome:r--
group::r--
mask::r--
other::r--
```

The exact output may contain additional metadata, but the important entries are:

```text
owner: root
group: root
user:anita:---
user:jerome:r--
other::r--
```

---

#  Understanding the Final Permissions

The final configuration should look like:

```text
                 /etc/hosts
                     │
       ┌─────────────┼─────────────┐
       │             │             │
     Owner         Group         Others
     root          root            r--
     rw-           r--             │
                                   │
                    ┌──────────────┘
                    │
             ACL User Permissions
                    │
             ┌──────┴──────┐
             │             │
           anita         jerome
            ---            r--
```

---

# ️ Why ACLs Are Required

Normal Linux permissions only provide three basic permission categories:

```text
Owner
Group
Others
```

For example:

```text
rw-r--r--
```

But this task requires different permissions for two specific users:

```text
anita  → ---
jerome → r--
```

Both users could potentially fall under the same `Others` category, but standard permissions cannot distinguish between them.

Therefore, **ACLs are required**.

ACLs allow additional user-specific permissions beyond the traditional owner/group/others model.

---

#  Complete Command Set

The complete solution can be executed as:

```bash
ssh banner@stapp03

sudo -i

chown root:root /etc/hosts

chmod 644 /etc/hosts

setfacl -m u:anita:--- /etc/hosts

setfacl -m u:jerome:r-- /etc/hosts

getfacl /etc/hosts
```

---

#  Verification Checklist

Before completing the task, verify:

- [x] `/etc/hosts` owner is `root`
- [x] `/etc/hosts` group is `root`
- [x] Others have read-only permission
- [x] `anita` has no permissions
- [x] `jerome` has read-only permission
- [x] ACL configuration is visible using `getfacl`

---

#  Important Commands Learned

| Command | Purpose |
|---|---|
| `ls -l` | View standard file permissions |
| `chown` | Change file owner/group |
| `chmod` | Change standard Linux permissions |
| `setfacl` | Create/modify ACL entries |
| `getfacl` | View ACL configuration |

---

#  Real-World DevOps Use Case

ACLs are useful when standard Linux permissions are not granular enough.

For example, suppose:

```text
/opt/application/config.yaml
```

is owned by:

```text
root:devops
```

You may want:

```text
root        → read/write
devops      → read
developer1  → read
developer2  → no access
auditor     → read-only
```

Using only:

```text
owner/group/others
```

cannot express all of these requirements cleanly.

ACLs allow administrators to provide **fine-grained user-level access control** without changing the fundamental ownership of the file.

---

#  Key Takeaway

The main concept demonstrated by this task is:

```text
Linux Permissions
       +
     ACLs
       ↓
Fine-Grained Access Control
```

The most important commands to remember are:

```bash
chown
chmod
setfacl
getfacl
```

Especially:

```bash
setfacl -m u:<username>:<permissions> <file>
```

Example:

```bash
setfacl -m u:jerome:r-- /etc/hosts
```

And to inspect the result:

```bash
getfacl /etc/hosts
```

---

##  References

- `setfacl` Linux manual: [setfacl documentation](https://man7.org/linux/man-pages/man1/setfacl.1.html?utm_source=chatgpt.com)
- `getfacl` Linux manual: [getfacl documentation](https://man7.org/linux/man-pages/man1/getfacl.1.html?utm_source=chatgpt.com)