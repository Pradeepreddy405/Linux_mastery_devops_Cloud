# Copy User-Owned Files While Preserving Directory Structure

##  Lab Overview

Due to an accidental data mix-up in the Nautilus environment, user data was unintentionally mingled under:

```text
/home/usersdata
```

The requirement was to locate all **files owned by the user `ravi`**, excluding directories, and copy those files to:

```text
/ecommerce
```

The original directory structure had to be preserved.

---

##  Task Requirements

| Requirement | Value |
|---|---|
| Target Server | `App Server 1` |
| Hostname | `stapp01` |
| Source Directory | `/home/usersdata` |
| File Owner | `ravi` |
| Destination Directory | `/ecommerce` |
| Include | Regular files |
| Exclude | Directories |
| Preserve | Relative directory structure |

---

#  Infrastructure

### App Server 1

```text
Hostname: stapp01
User: tony
Purpose: Nautilus Application 1
```

Connect from the Jump Host:

```bash
ssh tony@stapp01
```

---

#  Step 1: Verify the Target Server

Before making any changes, verify that we are connected to the correct server:

```bash
hostname
whoami
```

Expected:

```text
stapp01
tony
```

This prevents accidentally modifying files on the wrong server.

---

#  Step 2: Check the Source Directory

Verify that the source directory exists:

```bash
ls -ld /home/usersdata
```

Check the destination:

```bash
ls -ld /ecommerce
```

If `/ecommerce` does not exist:

```bash
sudo mkdir -p /ecommerce
```

---

#  Step 3: Locate Ravi's Files

Before copying anything, identify all regular files owned by `ravi`:

```bash
sudo find /home/usersdata -type f -user ravi -print
```

### Command breakdown

```text
find /home/usersdata
```

Search recursively under `/home/usersdata`.

```text
-type f
```

Select **regular files only**.

This is important because the requirement explicitly says to exclude directories.

```text
-user ravi
```

Select files owned by `ravi`.

```text
-print
```

Display the matching file paths.

---

#  Example Source Structure

Suppose the source contains:

```text
/home/usersdata/
├── orders/
│   ├── order1.txt
│   └── order2.txt
├── products/
│   ├── data/
│   │   └── products.csv
│   └── inventory.txt
└── backup/
    └── backup.tar
```

If the files owned by `ravi` are:

```text
/home/usersdata/orders/order1.txt
/home/usersdata/products/data/products.csv
/home/usersdata/backup/backup.tar
```

the destination should become:

```text
/ecommerce/
├── orders/
│   └── order1.txt
├── products/
│   └── data/
│       └── products.csv
└── backup/
    └── backup.tar
```

The `/home/usersdata` directory itself should **not** become part of the destination path.

---

#  Step 4: Copy Files While Preserving Structure

Use the following command:

```bash
sudo find /home/usersdata -type f -user ravi -print0 | while IFS= read -r -d '' file; do
    rel="${file#/home/usersdata/}"
    sudo mkdir -p "/ecommerce/$(dirname "$rel")"
    sudo cp "$file" "/ecommerce/$rel"
done
```

---

##  Command Explanation

### Find matching files

```bash
find /home/usersdata -type f -user ravi -print0
```

This finds only regular files owned by `ravi`.

`-print0` safely handles filenames containing spaces, tabs, or other special characters.

---

### Read each file

```bash
while IFS= read -r -d '' file
```

Processes each pathname safely.

---

### Remove the source prefix

```bash
rel="${file#/home/usersdata/}"
```

For example:

```text
/home/usersdata/orders/order1.txt
```

becomes:

```text
orders/order1.txt
```

---

### Create the required directory

```bash
sudo mkdir -p "/ecommerce/$(dirname "$rel")"
```

Creates the destination directory structure if it doesn't already exist.

---

### Copy the file

```bash
sudo cp "$file" "/ecommerce/$rel"
```

Copies the file to the corresponding location under `/ecommerce`.

---

#  Step 5: Verify the Copy

First, list the source files:

```bash
sudo find /home/usersdata -type f -user ravi -printf '%P\n' | sort
```

Then list the destination files:

```bash
sudo find /ecommerce -type f -printf '%P\n' | sort
```

The relative paths should match.

---

#  Step 6: Verify Ownership

Check the ownership of the copied files:

```bash
sudo find /ecommerce -type f -printf '%u:%g %p\n'
```

If ownership needs to remain exactly as the source, verify individual files with:

```bash
sudo stat /home/usersdata/path/to/file
sudo stat /ecommerce/path/to/file
```

---

#  Operational Workflow

This task follows a standard Linux administration workflow:

```text
┌─────────────────────────┐
│         CHECK           │
│                         │
│ hostname                │
│ whoami                  │
│ source directory        │
│ destination directory   │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│        IDENTIFY         │
│                         │
│ find files              │
│ owned by ravi           │
│ exclude directories     │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│         CHANGE          │
│                         │
│ Copy matching files     │
│ Preserve structure      │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│        VERIFY           │
│                         │
│ Compare source paths    │
│ with destination paths  │
└─────────────────────────┘
```

---

# ️ Important Linux Concepts

## `find -type f`

```bash
find /path -type f
```

Finds regular files only.

This is preferable to:

```bash
find /path
```

because the latter also returns directories and other filesystem objects.

---

## `find -user`

```bash
find /path -user ravi
```

Filters filesystem objects based on their owner.

Combined with:

```bash
-type f
```

we get:

```bash
find /path -type f -user ravi
```

which means:

> Find regular files owned by `ravi`.

---

## Why Preserve Directory Structure?

Preserving the structure prevents unrelated files with the same filename from being placed into a single directory.

For example:

```text
/home/usersdata/team1/report.txt
/home/usersdata/team2/report.txt
```

should remain:

```text
/ecommerce/team1/report.txt
/ecommerce/team2/report.txt
```

rather than:

```text
/ecommerce/report.txt
```

which could cause a filename collision.

---

#  Important Considerations

### 1. Don't use `cp *`

Avoid:

```bash
cp * /ecommerce
```

This does not satisfy the requirement because it doesn't filter files by owner.

### 2. Don't omit `-type f`

Avoid:

```bash
find /home/usersdata -user ravi
```

This can return directories owned by `ravi`.

The requirement explicitly says:

> Files excluding directories.

Therefore:

```bash
-type f
```

is required.

### 3. Don't blindly use `cp --parents`

For example:

```bash
cp --parents /home/usersdata/file.txt /ecommerce/
```

can produce:

```text
/ecommerce/home/usersdata/file.txt
```

That is different from preserving the structure **relative to `/home/usersdata`**:

```text
/ecommerce/file.txt
```

For this task, the relative-path approach is clearer and avoids unnecessary source-path components.

---

#  Verification Checklist

- [x] Connected to `stapp01`
- [x] Verified current user
- [x] Checked `/home/usersdata`
- [x] Checked `/ecommerce`
- [x] Filtered files by owner `ravi`
- [x] Excluded directories using `-type f`
- [x] Preserved relative directory structure
- [x] Verified destination files
- [x] Checked file ownership

---

#  Final Result

All regular files owned by:

```text
ravi
```

under:

```text
/home/usersdata
```

were identified and copied to:

```text
/ecommerce
```

while maintaining their directory structure relative to the source directory.

### Final mapping

```text
/home/usersdata/<path>/<file>
              ↓
/ecommerce/<path>/<file>
```

---

##  Key Takeaways

This lab demonstrates several important Linux administration skills:

- Recursive file searching with `find`
- Filtering files by ownership
- Excluding directories
- Safe handling of filenames
- Preserving directory structures
- Using `mkdir -p`
- Copying files with `cp`
- Post-change verification
- Applying the **Check → Identify → Change → Verify** operational workflow

These are basic but important filesystem operations that appear frequently in real-world **Linux administration, DevOps, SRE, backup, migration, and production-support work**.