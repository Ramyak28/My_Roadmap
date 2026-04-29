# Day 2: File Permissions & Ownership

> **Phase 1 — Linux, Networking & Cloud Basics** | Week 1 | 30 Minutes

---

## 📋 What You'll Learn Today

| Item | Detail |
|------|--------|
| **Topic** | File permissions & ownership |
| **Commands** | `chmod`, `chown`, `chgrp` |
| **Concept** | Octal notation (755, 644) |
| **Time** | 30 minutes |

---

## 🔑 Core Concepts

### What Are File Permissions?

In Linux, every file and directory has **3 types of permissions** assigned to **3 types of users**.

#### 3 User Types

| Symbol | Name | Description |
|--------|------|-------------|
| `u` | **Owner** | The user who created the file |
| `g` | **Group** | A group of users sharing access |
| `o` | **Others** | Everyone else on the system |

#### 3 Permission Types

| Symbol | Name | Octal Value | Meaning |
|--------|------|-------------|---------|
| `r` | Read | **4** | Can view the file's contents |
| `w` | Write | **2** | Can modify or delete the file |
| `x` | Execute | **1** | Can run the file as a program |
| `-` | None | **0** | Permission not granted |

---

### How to Read Permissions

Run `ls -l` to view permissions:

```
-rwxr-xr-- 1 john developers 1024 Apr 28 file.sh
```

Break it down character by character:

```
- rwx r-x r--
│  │   │   │
│  │   │   └── Others : r-- → read only
│  │   └────── Group  : r-x → read + execute
│  └────────── Owner  : rwx → read + write + execute
└───────────── Type   : -=file  d=directory  l=symlink
```

---

### Octal Notation Explained

Each permission type has a numeric value. **Add the values** for each user type to get a single digit.

```
r = 4
w = 2
x = 1
- = 0
```

#### Common Octal Combinations

| Octal | Symbolic | r | w | x | Calculation |
|-------|----------|---|---|---|-------------|
| **7** | `rwx` | ✅ | ✅ | ✅ | 4+2+1 = 7 |
| **6** | `rw-` | ✅ | ✅ | ❌ | 4+2+0 = 6 |
| **5** | `r-x` | ✅ | ❌ | ✅ | 4+0+1 = 5 |
| **4** | `r--` | ✅ | ❌ | ❌ | 4+0+0 = 4 |
| **0** | `---` | ❌ | ❌ | ❌ | 0+0+0 = 0 |

#### Decoding `chmod 755`

```
  7       5       5
  │       │       │
Owner   Group   Others
rwx     r-x     r-x
4+2+1   4+0+1   4+0+1
```

- **Owner (7):** `rwx` → Can read, write, and execute — full control
- **Group (5):** `r-x` → Can read and execute, but **cannot** modify
- **Others (5):** `r-x` → Can read and execute, but **cannot** modify

#### Decoding `chmod 644`

```
  6       4       4
  │       │       │
Owner   Group   Others
rw-     r--     r--
4+2+0   4+0+0   4+0+0
```

- **Owner (6):** `rw-` → Can read and write, but **cannot** execute
- **Group (4):** `r--` → Read only
- **Others (4):** `r--` → Read only

---

## 🛠️ Commands

### `chmod` — Change Permissions

```bash
# Syntax
chmod [options] <permissions> <filename>

# Set using octal notation
chmod 755 script.sh       # rwxr-xr-x  (scripts, directories)
chmod 644 config.txt      # rw-r--r--  (regular files)
chmod 600 id_rsa          # rw-------  (private keys)

# Set using symbolic notation
chmod +x script.sh        # Add execute for everyone
chmod u+x script.sh       # Add execute for owner only
chmod g-w file.txt        # Remove write from group
chmod a-x file.txt        # Remove execute from ALL users

# Apply recursively to a directory
chmod -R 755 /var/www/
```

### `chown` — Change Owner

```bash
# Syntax
chown [options] <owner>:<group> <filename>

# Change only the owner
chown john file.txt

# Change owner AND group at once
chown john:developers file.txt

# Change only the group (note the colon prefix)
chown :developers file.txt

# Apply recursively
chown -R john:developers /var/www/
```

### `chgrp` — Change Group

```bash
# Syntax
chgrp <group> <filename>

# Change group of a file
chgrp developers file.txt

# Apply recursively
chgrp -R developers /var/www/
```

---

## 🧪 Hands-On Task (20 Minutes)

Follow these steps in your Linux terminal (WSL or VM):

```bash
# Step 1 — Create a test file and check its default permissions
touch myfile.txt
ls -l myfile.txt
# Expected output: -rw-r--r-- (644 by default)

# Step 2 — Apply 755 and observe the change
chmod 755 myfile.txt
ls -l myfile.txt
# Expected output: -rwxr-xr-x

# Step 3 — Apply 644 and observe the change
chmod 644 myfile.txt
ls -l myfile.txt
# Expected output: -rw-r--r--

# Step 4 — Create a new user to practice chown
sudo useradd testuser

# Step 5 — Change the file's owner to testuser
sudo chown testuser myfile.txt
ls -l myfile.txt
# Owner column should now show: testuser

# Step 6 — Change it back to your user
sudo chown $USER myfile.txt
ls -l myfile.txt

# Step 7 — Apply permissions recursively to a directory
mkdir myproject
touch myproject/file1.txt myproject/file2.txt
chmod -R 755 myproject/
ls -l myproject/
```

**Expected final output:**
```
-rwxr-xr-x 1 youruser yourgroup 0 Apr 28 myproject/file1.txt
-rwxr-xr-x 1 youruser yourgroup 0 Apr 28 myproject/file2.txt
```

---

## 🎤 Interview Question & Full Answer

> **Q: What does `chmod 755` mean? Explain each digit.**

**Answer:**

`chmod 755` sets file permissions using **octal notation**, where each of the three digits represents permissions for a different user category — Owner, Group, and Others.

**The number system:** Each permission type has a fixed value — `read=4`, `write=2`, `execute=1`. You add the values together to get a single digit per user type.

**Breaking down 755:**

| Digit | User | Calculation | Symbolic | Meaning |
|-------|------|-------------|----------|---------|
| **7** | Owner | 4+2+1 | `rwx` | Full control — read, write, and execute |
| **5** | Group | 4+0+1 | `r-x` | Can read and run, but cannot modify |
| **5** | Others | 4+0+1 | `r-x` | Can read and run, but cannot modify |

**Real-world usage:** `chmod 755` is the standard permission for **executable scripts** and **web server directories**. The owner (typically a developer or root) can edit the script, while the system and other users can only run it — they cannot tamper with it.

**Contrast with `chmod 644`** — used for regular data files like config files or HTML:

| Digit | User | Calculation | Symbolic | Meaning |
|-------|------|-------------|----------|---------|
| **6** | Owner | 4+2+0 | `rw-` | Can read and edit, but not execute |
| **4** | Group | 4+0+0 | `r--` | Read only |
| **4** | Others | 4+0+0 | `r--` | Read only |

No one can execute a 644 file because it is treated as data, not a program.

---

## 📌 Quick Reference Card

| Octal | Symbolic | Common Use Case | Notes |
|-------|----------|-----------------|-------|
| `755` | `rwxr-xr-x` | Scripts, web directories | Standard for executables |
| `644` | `rw-r--r--` | Config files, HTML files | Standard for regular files |
| `600` | `rw-------` | SSH private keys (`~/.ssh/id_rsa`) | Only owner can read |
| `700` | `rwx------` | Private scripts | Only owner can use |
| `777` | `rwxrwxrwx` | ⚠️ Avoid in production | Full access for everyone — security risk |
| `400` | `r--------` | Read-only sensitive files | Even owner cannot write |

---

*Part of the **Cloud & DevOps Engineer Interview Preparation Roadmap** — 6 Months · 30 Minutes Per Day · 180 Daily Sessions*
