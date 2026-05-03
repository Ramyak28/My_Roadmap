## Day 5 — Bash scripting basics

**Phase:** 1 — Linux, Networking & Cloud Basics  
**Week:** 1 — Linux Fundamentals  
**Time:** 30 minutes

---

### What I Learned

#### What is a Bash script?
A **bash script** is a plain text file of Linux commands that the shell runs top to bottom. It automates tasks you would otherwise type manually. Every script starts with a **shebang** (`#!/bin/bash`) on line 1, which tells the OS to use bash to run it.

#### Special Variables

| Variable | Meaning | Example |
|---|---|---|
| `$1`, `$2` ... | Positional arguments passed to the script | `$1` = first argument given |
| `$#` | Total number of arguments passed | `2` means 2 args were given |
| `$@` | All arguments as a list | Used in loops: `for arg in $@` |
| `$?` | Exit code of the last command | `0` = success, non-zero = error |
| `$0` | The name of the script itself | `./count_files.sh` |

---

### Script Written: `count_files.sh`

Takes a directory as an argument, counts how many files are in it, and prints the result. Includes full error handling.

```bash
#!/bin/bash

if [ $# -eq 0 ]; then
  echo "Error: No directory provided."
  echo "Usage: $0 <directory>"
  exit 1
fi

DIR=$1

if [ ! -d "$DIR" ]; then
  echo "Error: '$DIR' is not a valid directory."
  exit 1
fi

COUNT=$(find "$DIR" -maxdepth 1 -type f | wc -l)

echo "Directory: $DIR"
echo "Number of files: $COUNT"
exit 0
```

---

### How to Run It

```bash
chmod +x count_files.sh        # make executable
./count_files.sh /etc          # valid directory
./count_files.sh               # error: no argument
./count_files.sh /fakepath     # error: bad directory
echo $?                        # check exit code: 0 = success, 1 = error
```

---

### Hands-On Task

1. Wrote `count_files.sh` manually in a terminal editor
2. Made it executable with `chmod +x`
3. Tested all three cases: valid directory, no argument, bad directory
4. Verified exit codes using `echo $?` after each run

---

### Interview Question

**Q: What is `$?`, `$#`, `$@` in bash? Explain with an example.**

**A:** These are special shell variables. `$?` holds the exit code of the last command — `0` means success, anything else means failure, which is how scripts chain logic and detect errors. `$#` holds the count of arguments passed to the script — if you run `./script.sh a b c`, then `$#` is `3`. `$@` holds all arguments as a list — useful when looping: `for arg in $@; do echo $arg; done` prints each argument on its own line.

---

### Key Takeaway

> Always validate your inputs at the top of a script using `$#` and `-d`. Use `exit 0` for success and `exit 1` for failure so other scripts or cron jobs can detect if something went wrong.
