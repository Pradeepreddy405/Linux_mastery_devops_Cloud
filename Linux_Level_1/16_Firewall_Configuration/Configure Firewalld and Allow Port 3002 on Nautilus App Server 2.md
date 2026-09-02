# Configure Firewalld and Allow Port 3002 on Nautilus App Server 2

## 📌 Task Description

The Nautilus system administrators deployed a web UI application for the backup utility on **Nautilus Application Server 2** in the **Stratos Datacenter**.

The application listens on:

```text
3002/tcp
```

The server must be configured with `firewalld` to allow incoming traffic to this port.

### Requirements

1. Install `firewalld`.
2. Enable and start the `firewalld` service.
3. Configure the `public` zone.
4. Allow incoming connections on `3002/tcp`.
5. Make the firewall rule permanent.

---

## 🏗️ Infrastructure

| Server | Hostname | User | Purpose |
|---|---|---|---|
| Application Server 1 | `stapp01` | `tony` | Nautilus Application 1 |
| **Application Server 2** | **`stapp02`** | **`steve`** | **Nautilus Application 2** |
| Application Server 3 | `stapp03` | `banner` | Nautilus Application 3 |
| LoadBalancer Server | `stlb01` | `loki` | Distributes HTTP traffic |
| Database Server | `stdb01` | `peter` | Nautilus Database |
| Storage Server | `ststor01` | `natasha` | Nautilus Storage |

> **Note:** This task only requires changes on `stapp02`.

---

# 🎯 Objective

Configure:

```text
Server       : stapp02
Service      : firewalld
Zone         : public
Port         : 3002/tcp
Persistence  : Permanent
```

Expected final state:

```text
firewalld → enabled and running
Zone      → public
Port      → 3002/tcp
```

---

# 🔧 Implementation

## Step 1: Connect to Application Server 2

```bash
ssh steve@stapp02
```

---

## Step 2: Install firewalld

For RHEL/CentOS-based systems:

```bash
sudo yum install firewalld -y
```

If `dnf` is used:

```bash
sudo dnf install firewalld -y
```

Verify the installation:

```bash
firewall-cmd --version
```

---

## Step 3: Enable and Start firewalld

```bash
sudo systemctl enable --now firewalld
```

Check the service:

```bash
sudo systemctl status firewalld
```

Expected:

```text
Active: active (running)
```

You can also verify:

```bash
sudo firewall-cmd --state
```

Expected:

```text
running
```

---

# Step 4: Check the Current Zone

Display the default zone:

```bash
sudo firewall-cmd --get-default-zone
```

Display active zones:

```bash
sudo firewall-cmd --get-active-zones
```

The required zone is:

```text
public
```

`firewall-cmd` provides commands for checking active zones and zone assignments.

---

# Step 5: Set the Default Zone to Public

```bash
sudo firewall-cmd --set-default-zone=public
```

Verify:

```bash
sudo firewall-cmd --get-default-zone
```

Expected:

```text
public
```

---

# Step 6: Allow Port 3002/tcp

Add port `3002/tcp` to the `public` zone permanently:

```bash
sudo firewall-cmd --zone=public --add-port=3002/tcp --permanent
```

The `--permanent` option stores the rule in the permanent configuration so that it survives a firewalld restart or system reboot.

---

# Step 7: Reload firewalld

Because the port was added to the permanent configuration, reload firewalld:

```bash
sudo firewall-cmd --reload
```

---

# 🔍 Verification

## Verify the Zone

```bash
sudo firewall-cmd --get-default-zone
```

Expected:

```text
public
```

---

## Verify Port 3002

```bash
sudo firewall-cmd --zone=public --list-ports
```

Expected:

```text
3002/tcp
```

---

## Verify the Complete Public Zone

```bash
sudo firewall-cmd --zone=public --list-all
```

Look for:

```text
ports: 3002/tcp
```

---

## Verify Permanent Configuration

```bash
sudo firewall-cmd --permanent --zone=public --list-ports
```

Expected:

```text
3002/tcp
```

This is important because firewalld maintains separate runtime and permanent configurations.

---

# ⚡ Quick Solution

If you already know the server and just need the commands:

```bash
ssh steve@stapp02

sudo yum install firewalld -y

sudo systemctl enable --now firewalld

sudo firewall-cmd --set-default-zone=public

sudo firewall-cmd --zone=public --add-port=3002/tcp --permanent

sudo firewall-cmd --reload
```

Verify:

```bash
sudo systemctl is-enabled firewalld
sudo systemctl is-active firewalld
sudo firewall-cmd --get-default-zone
sudo firewall-cmd --zone=public --list-ports
```

Expected:

```text
enabled
active
public
3002/tcp
```

---

# 🧠 Important Concepts

## Runtime vs Permanent Configuration

Firewalld has two configurations:

```text
Runtime
   ↓
Currently active firewall rules

Permanent
   ↓
Rules saved for future reloads/restarts
```

For example:

```bash
firewall-cmd --zone=public --add-port=3002/tcp
```

adds the port to the runtime configuration.

Whereas:

```bash
firewall-cmd --zone=public --add-port=3002/tcp --permanent
```

adds it to the permanent configuration.

Therefore, using `--permanent` is important for this task.

---

# 🔄 Why Do We Reload?

After adding a permanent rule:

```bash
sudo firewall-cmd --zone=public --add-port=3002/tcp --permanent
```

reload firewalld:

```bash
sudo firewall-cmd --reload
```

This applies the permanent configuration to the running firewall.

---

# 🔐 Why `3002/tcp`?

The application is listening on TCP port `3002`.

Therefore the firewall rule must specify:

```text
Port     : 3002
Protocol : TCP
```

The corresponding firewalld syntax is:

```bash
--add-port=3002/tcp
```

---

# 🚫 Common Mistakes

### Mistake 1: Forgetting `--permanent`

Incorrect for a persistent configuration:

```bash
firewall-cmd --zone=public --add-port=3002/tcp
```

Correct:

```bash
firewall-cmd --zone=public --add-port=3002/tcp --permanent
```

---

### Mistake 2: Using the wrong zone

The task specifically requires:

```text
public
```

Therefore use:

```bash
--zone=public
```

---

### Mistake 3: Forgetting to reload

After adding a permanent rule:

```bash
sudo firewall-cmd --reload
```

---

### Mistake 4: Modifying the wrong server

Only this server needs to be configured:

```text
stapp02
```

Do **not** configure `stapp01` or `stapp03` for this task.

---

# 📋 Final Checklist

- [x] Connected to `stapp02`
- [x] Installed `firewalld`
- [x] Enabled `firewalld`
- [x] Started `firewalld`
- [x] Default zone set to `public`
- [x] Allowed `3002/tcp`
- [x] Rule made permanent
- [x] Reloaded firewalld
- [x] Verified port
- [x] Verified zone

---

# ✅ Final State

The final configuration should be:

```text
Server       : stapp02
Firewall     : firewalld
Service      : enabled + running
Default Zone : public
Allowed Port : 3002/tcp
Persistence  : permanent
```

The backup web UI can now receive incoming TCP connections on port `3002`.

## 📚 References

- [firewalld — Open a Port or Service](https://firewalld.org/documentation/howto/open-a-port-or-service.html?utm_source=chatgpt.com)
- [firewall-cmd Manual](https://firewalld.org/documentation/man-pages/firewall-cmd.html?utm_source=chatgpt.com)