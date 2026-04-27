# Day 1 — Linux File System & Navigation

**Topic:** Linux File System Hierarchy (FHS) + Navigation Commands

---

## 📖 The Concept

Linux organizes everything into a **single tree** starting from `/` (called **root**). Unlike Windows which has `C:\`, `D:\` etc., Linux has one root and everything — including external drives, USB, network mounts — hangs under it.

Every directory has a specific purpose. This is called the **Filesystem Hierarchy Standard (FHS)**.

### Key Directories Every DevOps Engineer Must Know

| Directory | Purpose | Real-world Example |
|-----------|---------|-------------------|
| `/` | Root of the entire file system | Everything starts here |
| `/etc` | Configuration files | `/etc/nginx/nginx.conf`, `/etc/ssh/sshd_config` |
| `/var` | Variable data — logs, databases, mail | `/var/log/nginx/access.log` |
| `/tmp` | Temporary files (cleared on reboot) | Scripts write temp files here |
| `/home` | User home directories | `/home/ubuntu`, `/home/ec2-user` |
| `/root` | Home directory of the root user | Separate from `/home` |
| `/bin` | Essential binaries — ls, cp, mv | Basic commands available to all users |
| `/usr/bin` | User-installed binaries | Installed programs like nginx, python3 |
| `/sbin` | System binaries — only root uses these | ifconfig, fdisk, iptables |
| `/opt` | Optional/third-party software | `/opt/aws`, `/opt/google` |
| `/proc` | Virtual filesystem — live kernel data | `/proc/cpuinfo`, `/proc/meminfo` |
| `/dev` | Device files — disks, terminals | `/dev/sda` (disk), `/dev/null` |
| `/mnt` / `/media` | Mount points for external drives | Mounting an EBS volume here |

---

## 🛠️ Hands-On Task (20 min)

You need a Linux terminal. Use one of these:
- WSL on Windows (Windows Subsystem for Linux)
- EC2 instance on AWS free tier
- Any Ubuntu/CentOS VM

### Step 1 — Explore the root directory
```bash
ls /
```
You'll see all the top-level directories. Match them to the table above.

### Step 2 — Navigate and explore key directories
```bash
ls /etc        # See all config files
ls /var/log    # See all log files
ls /tmp        # Usually empty or has temp files
ls /home       # Your user directories
```

### Step 3 — Check where you are and move around
```bash
pwd            # Print Working Directory — shows your current location
cd /etc        # Change to /etc
pwd            # Confirm you're in /etc
cd ~           # Go back to your home directory
pwd            # Should show /home/yourusername or /root
cd -           # Go back to the previous directory (/etc)
```

### Step 4 — The `find` command (critical for interviews)
```bash
find /etc -name "*.conf"                  # Find all .conf files in /etc
find /var/log -name "*.log"               # Find all log files
find /tmp -type f                         # Find all regular files in /tmp
find / -name "sshd_config" 2>/dev/null   # Find sshd_config, suppress errors
```
> The `2>/dev/null` part redirects error messages (Permission denied etc.) to nowhere — you'll see this constantly in real shell scripts.

### Step 5 — The `tree` command (if available)
```bash
tree /etc -L 1    # Show /etc directory structure, 1 level deep
```
If `tree` is not installed:
- Ubuntu: `sudo apt install tree`
- Amazon Linux: `sudo yum install tree`

### Step 6 — Check real-world config files
```bash
cat /etc/hostname          # Your machine's hostname
cat /etc/hosts             # Local DNS — maps hostnames to IPs
cat /etc/os-release        # What Linux distro you are running
cat /proc/cpuinfo          # CPU info from the kernel (live data)
cat /proc/meminfo          # Memory info from the kernel
```

---

## 🎯 Interview Question & Complete Answer

### Q: What is the difference between `/etc`, `/var`, and `/tmp`?

**Answer:**

These three directories serve completely different purposes in the Linux filesystem hierarchy.

**`/etc`** stands for "et cetera" and is where all **system-wide configuration files** live. These are files that control how services and applications behave — for example, `/etc/nginx/nginx.conf` configures nginx, `/etc/ssh/sshd_config` controls SSH access settings, and `/etc/hosts` maps hostnames to IP addresses locally. The key characteristic of `/etc` is that these files are **static** — they don't change during normal operation. You edit them manually or via a configuration management tool like Ansible or Puppet, and then restart the service to apply the change.

**`/var`** stands for "variable" and is where data that **changes constantly during runtime** is stored. The most important subdirectory here is `/var/log`, which contains all system and application log files — for example, `/var/log/syslog`, `/var/log/nginx/access.log`, and `/var/log/auth.log`. Databases also store their data files under `/var/lib`. The critical thing about `/var` is that it **grows over time**, so in production you monitor disk usage here closely. A common incident is `/var/log` filling up the disk and crashing services — that's exactly why we set up log rotation.

**`/tmp`** is for **temporary files** that applications or scripts create during execution. The key property of `/tmp` is that it is **cleared on every reboot**. So if a script writes a temp file to `/tmp`, it's gone after the server restarts. Never store anything important here. In CI/CD pipelines, `/tmp` is often used as scratch space for builds. Some Linux systems also use a `tmpfs` filesystem for `/tmp`, which means it lives entirely in RAM — making reads and writes very fast but also meaning the data is never written to disk at all.

**In summary:** `/etc` is configuration (static), `/var` is runtime data that persists and grows (especially logs), and `/tmp` is temporary scratch space that disappears on reboot.

---

## 💡 Bonus Questions

### Q: What is `/proc` and is it a real directory?

`/proc` is a **virtual filesystem** — it doesn't exist on disk at all. The Linux kernel generates its contents in memory on-demand. When you read `/proc/cpuinfo`, the kernel creates that file on the fly. It's useful for reading live system state: CPU cores, memory usage, running processes, kernel parameters. For example, `cat /proc/loadavg` shows the current load average in real time. DevOps engineers use it to tune kernel parameters via `sysctl`, which reads from `/proc/sys/`.

### Q: What does `2>/dev/null` mean?

In Linux, every process has 3 standard streams:
- **stdin** (0) — input
- **stdout** (1) — output
- **stderr** (2) — error messages

The `2>` redirects **stderr** to `/dev/null`, which is a special device file that discards everything written to it — like a black hole. So `find / -name "sshd_config" 2>/dev/null` runs the find command but throws away all the "Permission denied" error messages, showing you only the results. You'll write this constantly in bash scripts.


