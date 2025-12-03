---
layout: single
title: "Linux Server Hardening (Baseline)"
permalink: /projects/linux-server-hardening/
author_profile: false
---

## Project Overview

This project focuses on applying a basic hardening baseline to a Linux server.  
The goal is to reduce the attack surface, improve SSH security, and enforce simple but effective controls that are realistic for a small production or lab environment.

---

## Objective

From a defensive/security engineering point of view, I wanted to:

- Apply a minimal but solid hardening baseline to a Linux host.
- Secure SSH access (no password login, no root login over SSH).
- Configure a basic firewall policy.
- Add simple protection against brute-force attempts (fail2ban or similar).
- Document the changes in a clear and reproducible way.

---

## Environment

- Linux VM (e.g. Ubuntu Server / Debian-based distro)
- Non-root user with sudo
- SSH server enabled
- Basic package manager (apt) available

---

## Hardening Steps

### 1. User and SSH configuration

- Created and used a non-root user for daily operations.
- Disabled direct root login over SSH:

```bash
sudo nano /etc/ssh/sshd_config
```

# In the config file:
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
Restarted SSH:

```bash
sudo systemctl restart ssh
```

*Effect:* Only key-based authentication is allowed and root cannot log in directly via SSH.

### 2. Firewall baseline

Configured a simple default-deny policy using ufw:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow OpenSSH
sudo ufw enable
```

*Effect:* Only SSH is exposed; all other incoming connections are blocked by default unless explicitly allowed.

### 3. Basic protection against brute-force

Installed and enabled fail2ban:

```bash
sudo apt update
sudo apt install fail2ban
```

Created a simple jail configuration for SSH:

```bash
sudo nano /etc/fail2ban/jail.local
```

Example content:

```
[sshd]
enabled  = true
port     = ssh
filter   = sshd
logpath  = /var/log/auth.log
maxretry = 5
bantime  = 600
```
Restarted the service:

```bash
sudo systemctl restart fail2ban
```

*Effect:* Repeated failed SSH attempts from the same IP are temporarily banned.

### 4. System updates and basic hygiene

- Updated installed packages:

```bash
sudo apt update && sudo apt upgrade -y
```
- Removed unused packages and services that were not needed in the environment.

Effect:
Reduces exposure to known vulnerabilities and unnecessary services.

---

## Result

After applying the baseline hardening:

- SSH access is restricted to key-based authentication with no root login.
- The firewall blocks all unsolicited inbound traffic except SSH.
- Brute-force attempts against SSH are detected and banned automatically.
- The system is kept up-to-date and unnecessary components are reduced.

This is not a full CIS benchmark implementation, but it is a realistic starting point
for improving the security posture of a small server or lab environment.

---

## What I Learned

- How to move from “default” Linux installation to a more secure baseline.
- How SSH configuration directly impacts the attack surface.
- How simple tools like ufw and fail2ban can provide meaningful protection.
- How to document hardening steps so they can be repeated or automated later.

---
