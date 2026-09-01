# Configure Crontab Access on App Server 2

##  Task Description

The Nautilus project team wants to restrict access to `crontab` for security and compliance purposes.

Configure **App Server 2 (`stapp02`)** with the following requirements:

- Allow `jim` to create and update cron jobs.
- Deny `ryan` access to `crontab`.

---

##  Server Details

| Server | Hostname | User |
|---|---|---|
| Application Server 2 | `stapp02` | `steve` |

---

##  Linux Mechanism Used

Linux cron access can be controlled using:

```text
/etc/cron.allow
/etc/cron.deny
```

### `/etc/cron.allow`

This acts as a **whitelist**.

If `/etc/cron.allow` exists, only users listed inside this file can use `crontab`.

For this task:

```text
/etc/cron.allow
└── jim
```

Therefore:

```text
jim  → Allowed
ryan → Denied
```

---

#  Step 1: Connect to App Server 2

From the jump host:

```bash
ssh steve@stapp02
```

Switch to root if required:

```bash
sudo -i
```

---

#  Step 2: Verify Users

Before making any changes, verify that both users exist:

```bash
id jim
id ryan
```

Expected result:

```text
uid=... (jim) ...
uid=... (ryan) ...
```

---

#  Step 3: Check Existing Cron Access Configuration

Do **not** blindly overwrite the configuration.

Check whether the access-control files already exist:

```bash
ls -l /etc/cron.allow /etc/cron.deny 2>/dev/null
```

Check their contents:

```bash
cat /etc/cron.allow 2>/dev/null
cat /etc/cron.deny 2>/dev/null
```

---

#  Step 4: Verify Current Access

Test `jim`:

```bash
sudo -u jim crontab -l
```

If the output is:

```text
no crontab for jim
```

this means `jim` **has permission to use crontab**, but does not currently have a cron job.

Test `ryan`:

```bash
sudo -u ryan crontab -l
```

Expected when access is denied:

```text
You (ryan) are not allowed to use this program (crontab)
```

---

#  Step 5: Configure `/etc/cron.allow`

If the configuration is not already correct, create or update `/etc/cron.allow`:

```bash
echo jim > /etc/cron.allow
```

Or using `sudo`:

```bash
sudo sh -c 'echo jim > /etc/cron.allow'
```

Set appropriate permissions:

```bash
chmod 644 /etc/cron.allow
```

Verify:

```bash
cat /etc/cron.allow
```

Expected:

```text
jim
```

---

#  Step 6: Verify `jim`

Run:

```bash
sudo -u jim crontab -l
```

Expected:

```text
no crontab for jim
```

This is **not an error indicating denied access**.

It means:

> `jim` is allowed to use `crontab`, but currently has no crontab.

You can also verify with:

```bash
sudo -u jim crontab -e
```

The editor should open successfully.

---

#  Step 7: Verify `ryan`

Run:

```bash
sudo -u ryan crontab -l
```

Expected:

```text
You (ryan) are not allowed to use this program (crontab)
```

This confirms that `ryan` is denied access.

---

#  Do We Need to Restart Cron?

**No.**

Changing:

```text
/etc/cron.allow
```

or:

```text
/etc/cron.deny
```

does **not require restarting the cron service**.

The access restriction is checked when the user executes `crontab`.

Therefore:

```text
Modify configuration
        ↓
Verify access
        ↓
Done
```

No restart is necessary.

---

#  Final Verification

Run:

```bash
cat /etc/cron.allow
```

Expected:

```text
jim
```

Then:

```bash
sudo -u jim crontab -l
```

Expected:

```text
no crontab for jim
```

And:

```bash
sudo -u ryan crontab -l
```

Expected:

```text
You (ryan) are not allowed to use this program (crontab)
```

---

#  Final Configuration

| User | Crontab Access | Verification |
|---|---|---|
| `jim` | ✅ Allowed | `no crontab for jim` |
| `ryan` | ❌ Denied | `You (ryan) are not allowed...` |

---

##  Important Linux Concept

Remember this rule:

```text
/etc/cron.allow → Whitelist
/etc/cron.deny   → Blacklist
```

When `/etc/cron.allow` exists:

> **Only users listed in `/etc/cron.allow` can use `crontab`.**

For example:

```text
/etc/cron.allow

jim
```

means:

```text
jim   → ✅ Allowed
ryan  → ❌ Denied
john  → ❌ Denied
tony  → ❌ Denied
```

This makes `/etc/cron.allow` the appropriate choice when the requirement is:

> "Only these specific users should be allowed to manage cron jobs."

---

##  Result

The crontab access policy on **App Server 2 (`stapp02`)** is configured so that:

```text
jim  → ALLOWED
ryan → DENIED
```

No cron service restart is required.