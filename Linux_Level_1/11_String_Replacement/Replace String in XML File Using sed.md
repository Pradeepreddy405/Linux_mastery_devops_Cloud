# Replace String in XML File Using `sed`

##  Task Description

At **xFusionCorp Industries**, the Stratos Datacenter jump host contains an XML template file used by the Nautilus application.

The task is to replace **all occurrences** of the string:

```text
Sample
```

with:

```text
LUSV
```

inside:

```text
/root/nautilus.xml
```

---

##  Infrastructure

| Server | Hostname | User | Purpose |
|---|---|---|---|
| Jump Host Server | `jump-host` | `thor` | Provides secure access to Stratos Datacenter |

---

##  Objective

Modify `/root/nautilus.xml` so that every occurrence of:

```text
Sample
```

is replaced with:

```text
LUSV
```

---

##  Solution

### Step 1: Verify the file

```bash
sudo ls -l /root/nautilus.xml
```

Optionally inspect the contents:

```bash
sudo cat /root/nautilus.xml
```

---

### Step 2: Replace all occurrences

Run:

```bash
sudo sed -i 's/Sample/LUSV/g' /root/nautilus.xml
```

### Command Breakdown

```text
sudo
```

Runs the command with root privileges because `/root/nautilus.xml` is accessible only to root.

```text
sed
```

A Linux stream editor used for searching and modifying text.

```text
-i
```

Edits the file **in place** instead of only displaying the modified output.

```text
s/Sample/LUSV/g
```

This is the substitution expression:

| Part | Meaning |
|---|---|
| `s` | Substitute |
| `Sample` | String to search for |
| `LUSV` | Replacement string |
| `g` | Replace all occurrences on each line |

```text
/root/nautilus.xml
```

The XML file being modified.

---

##  Step 3: Verify the Replacement

Check whether any `Sample` strings remain:

```bash
sudo grep -n "Sample" /root/nautilus.xml
```

If there is **no output**, all occurrences have been replaced.

Now verify that `LUSV` exists:

```bash
sudo grep -n "LUSV" /root/nautilus.xml
```

---

##  Complete Command Sequence

```bash
sudo ls -l /root/nautilus.xml

sudo sed -i 's/Sample/LUSV/g' /root/nautilus.xml

sudo grep -n "Sample" /root/nautilus.xml

sudo grep -n "LUSV" /root/nautilus.xml
```

---

##  Important Linux Concept

The command:

```bash
sed -i 's/Sample/LUSV/g' /root/nautilus.xml
```

is particularly useful in **DevOps automation**.

Instead of manually opening configuration files and changing values, `sed` allows scripts and automation tools to modify configuration files automatically.

### Common DevOps Use Cases

- Updating application configuration
- Changing environment-specific values
- Modifying Nginx/Apache configuration
- Updating CI/CD configuration files
- Docker image build scripts
- Shell automation
- Server provisioning
- Configuration management

For example:

```bash
sed -i 's/ENV=development/ENV=production/g' application.conf
```

This automatically changes the environment from development to production.

---

##  Key Takeaways

- `sed` is a powerful Linux text-processing utility.
- `-i` modifies a file directly.
- `s/A/B/g` replaces all occurrences of `A` with `B`.
- `sudo` is required when the target file requires root privileges.
- Always **verify your changes** after modifying configuration files.

---

##  Expected Result

The file:

```text
/root/nautilus.xml
```

should contain `LUSV` wherever `Sample` previously appeared, with **no remaining occurrences of `Sample`**.