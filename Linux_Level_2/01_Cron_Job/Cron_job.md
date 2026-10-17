# Linux Cron Job Configuration on CentOS Stream 9

## Lab Overview

**Project:** Nautilus — Stratos Datacenter  
**Category:** Linux Administration | DevOps | Task Automation  
**Target Servers:** `stapp01`, `stapp02`, `stapp03`  
**Operating System:** CentOS Stream 9  
**Package:** `cronie`  
**Service:** `crond`

## Objective

Install the `cronie` package on all three Nautilus application servers, start and enable the cron daemon, and configure the following scheduled task for the root user:

```cron
*/5 * * * * echo hello > /tmp/cron_text
```

The cron job executes every five minutes and writes `hello` to `/tmp/cron_text`.

## Infrastructure Details

| Server | Login User | Purpose |
|---|---|---|
| `stapp01` | `tony` | Application Server 1 |
| `stapp02` | `steve` | Application Server 2 |
| `stapp03` | `banner` | Application Server 3 |

> Perform the steps on all three application servers. This documentation uses `stapp02` as the example.

## 1. Understand Cron and Cronie

**Cron** is a time-based job scheduler in Linux. It runs commands or scripts automatically according to a defined schedule.

**Cronie** is an implementation of the cron daemon used on many RHEL-family Linux distributions, including CentOS Stream.

Common DevOps use cases include:

- Automated log cleanup and rotation tasks
- Scheduled backup scripts
- Periodic health checks
- Temporary file cleanup
- Scheduled reports and maintenance scripts
- Recurring application maintenance jobs

## 2. Check Whether Cronie Is Installed

Check the installed package:

```bash
rpm -q cronie
```

If the package is not installed, the output will be similar to:

```text
package cronie is not installed
```

If installed, the command displays the package name and version.

## 3. Identify the Package Manager

Check which package managers are available:

```bash
command -v dnf yum apt
```

On CentOS Stream 9, `dnf` is the standard package manager. The `yum` command may also be available as a compatibility interface.

Check the operating system:

```bash
cat /etc/os-release
```

## 4. Check Whether the Package Is Available

Before installing software, search the configured repositories.

### Search for the package

```bash
sudo dnf search cronie
```

Example output:

```text
cronie.x86_64 : Cron daemon for executing programs at set times
```

### List available packages

```bash
dnf list available cronie
```

### Display package information

```bash
dnf info cronie
```

### Check enabled repositories

```bash
sudo dnf repolist
```

If the package is not found, investigate repository configuration and metadata before attempting installation.

**Important distinction:**

- `rpm -q cronie` checks whether the package is installed.
- `dnf search cronie` searches package names and descriptions.
- `dnf list available cronie` lists matching packages available for installation.
- `dnf info cronie` displays package details.

## 5. Install Cronie

Install the package on each application server:

```bash
sudo dnf install -y cronie
```

Verify the installation:

```bash
rpm -q cronie
```

Example output:

```text
cronie-1.5.7-16.el9.x86_64
```

The exact package version may vary.

## 6. Identify the Correct Service Name

The package name and systemd service name are not necessarily identical.

Although the package is named `cronie`, its service is named `crond`.

### Search installed service unit files

```bash
systemctl list-unit-files | grep -i cron
```

Expected result includes:

```text
crond.service
```

### Find service files provided by the package

```bash
rpm -ql cronie | grep -E '\.service$'
```

### Check the correct service

```bash
sudo systemctl status crond
```

Using `cronie` instead of `crond` may return:

```text
Unit cronie.service could not be found.
```

This does not mean the package installation failed. It means the wrong service name was used.

## 7. Start and Enable the Cron Service

Run:

```bash
sudo systemctl enable --now crond
```

This command:

- Starts the cron daemon immediately.
- Enables the service to start automatically at boot.

Check the service status:

```bash
sudo systemctl status crond
```

Check the active state:

```bash
systemctl is-active crond
```

Expected output:

```text
active
```

Check whether the service is enabled at boot:

```bash
systemctl is-enabled crond
```

Expected output:

```text
enabled
```

## 8. Configure the Cron Job for Root

The requirement is to create a scheduled task specifically for the root user.

### Open the root crontab

```bash
sudo crontab -e
```

Add the following line:

```cron
*/5 * * * * echo hello > /tmp/cron_text
```

Save and exit the editor.

### Verify the root crontab

```bash
sudo crontab -l -u root
```

Expected output:

```cron
*/5 * * * * echo hello > /tmp/cron_text
```

**Important:** Running `crontab -e` as an ordinary user edits that user's crontab. Using `sudo crontab -e` edits root's crontab.

## 9. Understand the Cron Schedule

The cron expression is:

```cron
*/5 * * * * echo hello > /tmp/cron_text
```

| Field | Value | Meaning |
|---|---|---|
| Minute | `*/5` | Every five minutes |
| Hour | `*` | Every hour |
| Day of month | `*` | Every day of the month |
| Month | `*` | Every month |
| Day of week | `*` | Every day of the week |

The job runs at minute `00`, `05`, `10`, `15`, `20`, and so on, according to the server's local time.

## 10. Verify the Cron Job

Wait until the next five-minute execution interval.

Check the file:

```bash
sudo cat /tmp/cron_text
```

Expected output:

```text
hello
```

Inspect the file's modification time:

```bash
sudo stat /tmp/cron_text
```

The modification time should update when the scheduled job runs.

**Note:** The `>` redirection overwrites the file each time. The file may not exist until the first scheduled execution.

## 11. Troubleshooting

### Package is not found

```bash
sudo dnf repolist
sudo dnf makecache
sudo dnf search cronie
```

### Service unit is not found

```bash
rpm -q cronie
systemctl list-unit-files | grep -i cron
```

Use `crond`, not `cronie`, as the service name.

### Cron service is not running

```bash
sudo systemctl enable --now crond
sudo systemctl status crond
```

### Cron job does not execute

Check the root crontab:

```bash
sudo crontab -l -u root
```

Check the service:

```bash
systemctl is-active crond
```

Check the output file:

```bash
sudo ls -l /tmp/cron_text
sudo cat /tmp/cron_text
```

Inspect recent service logs:

```bash
sudo journalctl -u crond --since "15 minutes ago"
```

Depending on the system's logging configuration, cron messages may also appear in `/var/log/cron`.

## 12. Final Verification Checklist

Complete the following checks on `stapp01`, `stapp02`, and `stapp03`.

- [ ] Confirm the operating system and package manager.
- [ ] Check whether `cronie` is installed.
- [ ] Confirm package availability in configured repositories.
- [ ] Install `cronie`.
- [ ] Identify the service name as `crond`.
- [ ] Start the service and enable it at boot.
- [ ] Verify that the service is active.
- [ ] Add the required entry to the root crontab.
- [ ] Verify the root crontab.
- [ ] Confirm that `/tmp/cron_text` contains `hello` after execution.

## Key Takeaways

1. Check package installation before attempting to install a package.
2. Verify repository availability before installation.
3. Package names and service names can differ.
4. Use `systemctl enable --now` to start a service and enable it at boot.
5. Use `sudo crontab -e` to configure jobs for root.
6. Verify scheduled jobs by checking both the crontab and their actual output.
7. Repeat the configuration and verification on every required application server.

## Conclusion

This lab demonstrates Linux package management, repository discovery, systemd service management, cron scheduling, and root-level task automation on CentOS Stream 9.

These are foundational skills for DevOps engineers responsible for Linux server administration and recurring operational tasks.