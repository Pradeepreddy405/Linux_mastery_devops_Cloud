# Synchronize Timezone on Nautilus Application Servers

## 📌 Task Description

The timezone settings across the **Nautilus Application Servers** in the **Stratos Datacenter** were inconsistent with the local datacenter timezone.

The required timezone is:

```text
America/Jamaica
```

The objective is to configure all three application servers to use `America/Jamaica` as their system timezone.

> **Important:** No server reboot is required after changing the timezone.

---

## 🏗️ Infrastructure

| Server | Hostname | User | Required Timezone |
|---|---|---|---|
| Application Server 1 | `stapp01` | `tony` | `America/Jamaica` |
| Application Server 2 | `stapp02` | `steve` | `America/Jamaica` |
| Application Server 3 | `stapp03` | `banner` | `America/Jamaica` |

---

## 🎯 Objective

Configure the following servers:

```text
stapp01 → America/Jamaica
stapp02 → America/Jamaica
stapp03 → America/Jamaica
```

---

# 🔧 Solution

## 1. Application Server 1

Connect to the server:

```bash
ssh tony@stapp01
```

Check the current timezone:

```bash
timedatectl
```

Set the timezone:

```bash
sudo timedatectl set-timezone America/Jamaica
```

Verify:

```bash
timedatectl
```

Or:

```bash
timedatectl show --property=Timezone --value
```

Expected:

```text
America/Jamaica
```

---

## 2. Application Server 2

Connect:

```bash
ssh steve@stapp02
```

Check current timezone:

```bash
timedatectl
```

Set the required timezone:

```bash
sudo timedatectl set-timezone America/Jamaica
```

Verify:

```bash
timedatectl show --property=Timezone --value
```

Expected:

```text
America/Jamaica
```

---

## 3. Application Server 3

Connect:

```bash
ssh banner@stapp03
```

Check current timezone:

```bash
timedatectl
```

Set the required timezone:

```bash
sudo timedatectl set-timezone America/Jamaica
```

Verify:

```bash
timedatectl show --property=Timezone --value
```

Expected:

```text
America/Jamaica
```

---

# 🔍 Verification

Run the following command on each application server:

```bash
timedatectl show --property=Timezone --value
```

Expected result on all servers:

```text
America/Jamaica
```

You can also use:

```bash
timedatectl
```

Example:

```text
Local time: Wed 2026-09-02 06:00:00 EST
Universal time: Wed 2026-09-02 11:00:00 UTC
RTC time: Wed 2026-09-02 11:00:00
Time zone: America/Jamaica (EST, -0500)
System clock synchronized: yes
NTP service: active
RTC in local TZ: no
```

---

# 🌎 Listing Available Timezones

To list all available Linux timezones:

```bash
timedatectl list-timezones
```

To find Jamaica:

```bash
timedatectl list-timezones | grep Jamaica
```

Output:

```text
America/Jamaica
```

To search for America timezones:

```bash
timedatectl list-timezones | grep '^America/'
```

---

# 🧠 Important Commands

### Display current timezone

```bash
timedatectl
```

### Display only timezone

```bash
timedatectl show --property=Timezone --value
```

### List all timezones

```bash
timedatectl list-timezones
```

### Set timezone

```bash
sudo timedatectl set-timezone America/Jamaica
```

### Check system date and time

```bash
date
```

---

# 🔄 Is a Reboot Required?

**No.**

Changing the timezone using:

```bash
timedatectl set-timezone
```

takes effect immediately.

There is no need to:

```bash
reboot
```

or:

```bash
systemctl restart systemd
```

---

# 📝 Key Concept

Linux uses the **IANA Time Zone Database** for timezone configuration.

`America/Jamaica` is an IANA timezone identifier.

The command:

```bash
timedatectl set-timezone America/Jamaica
```

configures the system to use that timezone.

The configuration is represented through:

```text
/etc/localtime
```

You can inspect it with:

```bash
ls -l /etc/localtime
```

---

# ✅ Final Validation

| Server | Expected Timezone | Status |
|---|---|---|
| `stapp01` | `America/Jamaica` | ✅ |
| `stapp02` | `America/Jamaica` | ✅ |
| `stapp03` | `America/Jamaica` | ✅ |

## 🎉 Result

All Nautilus Application Servers are synchronized with the Stratos Datacenter's required timezone:

```text
America/Jamaica
```

No reboot was performed.