## Day 5 — Bash Scripting Basics

**Phase:** 1 — Linux, Networking & Cloud Basics  
**Week:** 1 — Linux Fundamentals  
**Time:** 30 minutes

---

### What I Learned

How to write a bash script that:
- Accepts a directory as a command-line argument
- Counts the number of files in it
- Handles errors using `if/else` and exits with correct exit codes

---

### Key Concepts

| Variable | Meaning | Example |
|----------|---------|---------|
| `$?` | Exit code of the last command (0 = success) | `ls /tmp; echo $?` → `0` |
| `$#` | Number of arguments passed to the script | `./script.sh a b` → `$#` is `2` |
| `$@` | All arguments as separate words | Used in `for arg in "$@"` loops |
| `$1`, `$2` | Individual positional arguments | `./script.sh /tmp` → `$1` is `/tmp` |

---

### Script Written Today

**File:** `count_files.sh`

```bash
#!/bin/bash
# count_files.sh — takes a directory as argument, counts files

if [ $# -eq 0 ]; then
  echo "Usage: $0 <directory>"
  exit 1
fi

DIR="$1"

if [ ! -d "$DIR" ]; then
  echo "Error: '$DIR' is not a directory"
  exit 1
fi

COUNT=$(ls -1 "$DIR" | wc -l)
echo "Files in $DIR: $COUNT"
exit 0
```

---

### How to Run

```bash
chmod +x count_files.sh

# Normal usage
./count_files.sh /etc

# Test error — no arguments
./count_files.sh

# Test error — fake path
./count_files.sh /fakepath

# Check exit code after any run
echo $?
```

---

### Interview Question Practiced

> *What is `$?`, `$#`, and `$@` in bash? Explain each with an example.*

**My answer:**
- `$?` — exit code of the last run command. `0` means success, anything else is an error. Useful to check if a command worked before moving on.
- `$#` — total count of arguments passed. If I run `./script.sh a b c`, then `$#` is `3`. Used to validate that the user passed required arguments.
- `$@` — all arguments as a list. Used in loops like `for arg in "$@"` to process each argument one by one.
