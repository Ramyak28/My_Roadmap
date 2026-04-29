## Day 3 — Linux Users, Groups & sudo

**Topic:** User and group management; privilege escalation with sudo

### Key concepts

- Every user has a **UID** (User ID). Root = UID 0. Regular users = UID 1000+.
- **Groups** allow shared permissions across multiple users.
- **`su`** — switches your entire session to another user.
- **`sudo`** — runs a single command with elevated privilege, then returns to your original user. Safer because it's temporary and logged.
- **`/etc/sudoers`** — defines who can use sudo and for which commands. Always edit with `visudo`.

### Key files

| File | Purpose |
|---|---|
| `/etc/passwd` | User accounts (username, UID, home, shell). World-readable. |
| `/etc/shadow` | Hashed passwords and expiry. Root-readable only. |
| `/etc/sudoers` | Sudo access rules. Edit with `visudo` only. |




### Commands practised

```bash
# Create user with home directory
sudo useradd -m -s /bin/bash devuser

# Set password
sudo passwd devuser

# Create group
sudo groupadd devteam

# Add user to group (append, don't replace)
sudo usermod -aG devteam devuser

# Verify
cat /etc/passwd | grep devuser
groups devuser
sudo cat /etc/shadow | grep devuser
```


## User Management

| Command | What it does |
|---|---|
| `useradd -m username` | Create a new user with a home directory (`-m`) |
| `useradd -m -s /bin/bash username` | Create user and set their default shell to bash |
| `passwd username` | Set or change a user's password |
| `userdel username` | Delete a user (keeps home directory) |
| `userdel -r username` | Delete user and remove their home directory |
| `usermod -aG groupname username` | Add user to a group without removing from other groups |
| `usermod -s /bin/bash username` | Change a user's default shell |
| `id username` | Show user's UID, GID, and all groups they belong to |
| `whoami` | Show the current logged-in username |
| `who` / `w` | Show who is currently logged in to the system |

---

## Group Management

| Command | What it does |
|---|---|
| `groupadd groupname` | Create a new group |
| `groupdel groupname` | Delete a group |
| `groups username` | List all groups a user belongs to |
| `gpasswd -a username groupname` | Add a user to a group |
| `gpasswd -d username groupname` | Remove a user from a group |

---

## Privilege Escalation

| Command | What it does |
|---|---|
| `sudo command` | Run a single command as root, then return to normal user |
| `sudo -i` | Open a root shell (login shell) |
| `sudo -l` | List what commands the current user is allowed to run with sudo |
| `su username` | Switch your entire session to another user |
| `su -` | Switch to root user (full login shell with root environment) |
| `visudo` | Safely edit `/etc/sudoers` (validates syntax before saving) |

---

## Key Files

| File | Purpose |
|---|---|
| `/etc/passwd` | All user accounts — username, UID, home directory, shell. World-readable. |
| `/etc/shadow` | Hashed passwords and password expiry settings. Root-readable only. |
| `/etc/group` | All groups and their members |
| `/etc/sudoers` | Defines who can use sudo and for which commands. Edit with `visudo` only. |

---

## Practical Examples

```bash
# Create a new user with home directory and bash shell
sudo useradd -m -s /bin/bash devuser

# Set a password for the new user
sudo passwd devuser

# Create a new group
sudo groupadd devteam

# Add devuser to devteam group (append, don't replace existing groups)
sudo usermod -aG devteam devuser

# Verify the user was created
cat /etc/passwd | grep devuser
# Output: devuser:x:1001:1001::/home/devuser:/bin/bash

# Verify group membership
groups devuser
# Output: devuser : devuser devteam

# Check the user's UID and GIDs
id devuser
# Output: uid=1001(devuser) gid=1001(devuser) groups=1001(devuser),1002(devteam)

# View the shadow file entry (requires sudo)
sudo cat /etc/shadow | grep devuser

# Edit the sudoers file safely
sudo visudo
```

---

## sudo vs su — Key Difference

| | `sudo` | `su` |
|---|---|---|
| Scope | One command only | Entire session |
| Returns to original user | Yes, automatically | No — you stay as the switched user |
| Password used | Your own password | The target user's password |
| Logged | Yes — every command is logged | Limited logging |
| Safer for production | ✅ Yes | ⚠️ Use with caution |

---

## Interview Answer — sudo vs su & /etc/sudoers

**Q: How does sudo differ from su? What is /etc/sudoers?**

**A:** `su` replaces your current terminal session with another user's session entirely — you become that user until you type `exit`. `sudo` elevates privilege for one specific command only, then immediately returns you to your original user. This makes `sudo` safer because the privilege is temporary and every command is logged.

`/etc/sudoers` is the configuration file that controls which users or groups are allowed to run `sudo`, and optionally restricts which commands they can run and on which machines. It must always be edited using `visudo` — never a plain text editor — because `visudo` validates the syntax before saving, preventing a broken file that could lock everyone out of root access.

---

*Part of the Cloud & DevOps Engineer Interview Preparation Roadmap — Month 1, Week 1, Day 3*
