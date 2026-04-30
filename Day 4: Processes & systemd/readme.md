# 📅 Day 4 — Processes & systemd

> **Phase 1: Linux, Networking & Cloud Basics** · Week 1: Linux Fundamentals · Thursday · 30 min

---

## 🎯 What You Will Learn

- What a **process** is and how Linux tracks running programs using a **PID (Process ID)**
- How to view, monitor, and kill processes using `ps`, `top`, `htop`, and `kill`
- What **systemd** is and why it is the backbone of service management on modern Linux
- How to start, stop, check, and enable services with `systemctl`

---

## 🕐 30-Minute Session Plan

| Time | Activity |
|------|----------|
| 0–5 min | Read the topic introduction below |
| 5–25 min | Run every command in the Hands-On Task section |
| 25–30 min | Answer the interview question out loud — record yourself or write it down |

---

## 📖 Topic Introduction

When Linux runs a program, it creates a **process** — an instance of that program in memory. Every process gets a unique **PID (Process ID)** so the OS can track and manage it.

**systemd** is the **init system** — it is PID 1 (the very first process Linux starts on boot). It is responsible for starting all other services (nginx, SSH, databases, etc.) and keeping them running. You interact with systemd through a command called `systemctl`.

---

## 💻 Hands-On Task

Open your Linux terminal (WSL, VM, or EC2 instance) and run these commands one by one:

### Viewing Processes

```bash
# Show a snapshot of all running processes
ps aux

# Columns explained:
# USER  — who owns the process
# PID   — unique process ID
# %CPU  — how much CPU it is using
# %MEM  — how much memory it is using
# COMMAND — what program is running
```

```bash
# Live, refreshing view of processes sorted by CPU usage
# Press 'q' to quit
top
```

```bash
# Better version of top with colors and mouse support
# Install first if needed:
sudo apt install htop   # Ubuntu/Debian
sudo yum install htop   # Amazon Linux / CentOS

htop
```

### Killing Processes

```bash
# Find the PID of a process (example: nginx)
ps aux | grep nginx

# Gracefully stop a process — asks it to clean up and exit
kill -15 <PID>

# Forcefully kill a process — the OS destroys it immediately, no cleanup
kill -9 <PID>
```

> ⚠️ Always try `kill -15` first. Only use `kill -9` if the process is frozen and not responding.

### Managing Services with systemctl

```bash
# Check the current status of a service
systemctl status ssh
systemctl status nginx
systemctl status cron

# Start a service
sudo systemctl start nginx

# Stop a service
sudo systemctl stop nginx

# Restart a service (stop then start)
sudo systemctl restart nginx

# Enable a service to start automatically on every boot
sudo systemctl enable nginx

# Disable auto-start on boot
sudo systemctl disable nginx
```

### ✅ Goal

Check at least **3 running services** using `systemctl status`. Try starting and stopping one (nginx is a good choice — install it with `sudo apt install nginx` if needed).

---

## 🔑 Key Concepts

| Term | Explanation |
|------|-------------|
| **PID** | Process ID — a unique number assigned to every running process by the OS |
| **ps aux** | Shows a static snapshot of all running processes at that moment |
| **top** | Live, refreshing view of running processes and resource usage |
| **htop** | Improved interactive version of top with color and mouse support |
| **SIGTERM (kill -15)** | Polite shutdown signal — process can clean up before exiting |
| **SIGKILL (kill -9)** | Forced kill — the OS terminates the process immediately, no cleanup |
| **systemd** | The init system (PID 1) that manages all services on a Linux system |
| **systemctl** | Command-line tool to interact with systemd |
| **enable** | Makes a service start automatically every time the system boots |
| **daemon** | A background service process (e.g., nginx, sshd, crond) |

---

## 🎤 Interview Question

> *"What is the difference between kill -9 and kill -15?"*

### Sample Answer

`kill -15` sends a **SIGTERM (Signal Terminate)** — it is a polite request to stop. The process receives the signal and can perform cleanup tasks like saving state, flushing buffers, or closing open connections before it exits gracefully.

`kill -9` sends **SIGKILL** — it instructs the Linux kernel to immediately destroy the process. The process cannot catch, block, or ignore this signal. There is no cleanup.

**Best practice:** Always try `kill -15` first. Use `kill -9` only as a last resort when a process is unresponsive or frozen.

---

## 📝 Commands Quick Reference

```bash
ps aux                        # List all running processes
top                           # Live process monitor (press q to quit)
htop                          # Better live monitor
kill -15 <PID>                # Graceful stop (SIGTERM)
kill -9 <PID>                 # Force kill (SIGKILL)
systemctl status <service>    # Check service status
systemctl start <service>     # Start a service
systemctl stop <service>      # Stop a service
systemctl restart <service>   # Restart a service
systemctl enable <service>    # Auto-start on boot
systemctl disable <service>   # Disable auto-start on boot
```

---


---

*Part of the [Cloud & DevOps Engineer Interview Preparation Roadmap](../README.md) · 6 Months · 30 Minutes Per Day*
