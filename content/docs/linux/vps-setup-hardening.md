---
title: "VPS Setup and Hardening"
date: 2026-05-15
draft: false
tags:
  - linux
  - vps
  - security
  - hardening
  - docker
summary: "Initial setup and hardening process for a personal Linux VPS — covers user management, SSH hardening, firewall, Fail2ban, automatic updates, and Docker."
---

## Overview

This guide covers the initial setup and hardening process for a personal Linux VPS running Ubuntu. The goal is to establish a secure, lightweight, and maintainable foundation ready for Docker workloads, self-hosted services, and infrastructure experiments.

### Target State

After completing this guide, the server will have:

- Non-root sudo user with SSH key authentication
- Root login and password authentication disabled
- UFW firewall enabled with minimal open ports
- Fail2ban brute-force protection
- Automatic security updates
- Docker installed and configured

### Prerequisites

- A fresh Ubuntu 22.04 LTS VPS (tested on Tencent Cloud, works on any provider)
- SSH access as `root`
- An SSH key pair on your local machine

> [!NOTE]
> All commands assume Ubuntu/Debian. Adjust package manager commands for other distributions.

---

## Setup Steps

{{% steps %}}

### Create a Non-Root User

Running everything as `root` is a security risk — a misconfigured command or compromised session has unrestricted access to the entire system. The standard practice is to create a regular user with `sudo` privileges and disable direct root access.

```bash
adduser sultan
usermod -aG sudo sultan
```

Verify group membership:

```bash
groups sultan
# sultan : sultan sudo
```

---

### Configure SSH Key Authentication

SSH key authentication replaces password-based login with a cryptographic key pair. The private key stays on your local machine; the public key is placed on the server. Without the private key, access is not possible — even with the correct username.

{{< details title="Don't have an SSH key yet? Generate one first." closed="true" >}}

Run this on your **local machine**:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

- `-t ed25519` — uses the Ed25519 algorithm, the modern recommended choice
- `-C` — adds a comment to identify the key (typically your email)

When prompted:
- **File location** — press Enter to accept the default (`~/.ssh/id_ed25519`)
- **Passphrase** — recommended; adds a second layer of protection if your private key is ever stolen

This creates two files:
- `~/.ssh/id_ed25519` — your **private key** (never share this)
- `~/.ssh/id_ed25519.pub` — your **public key** (this goes on the server)

{{< /details >}}

Copy your local public key to the server:

```bash
ssh-copy-id sultan@YOUR_SERVER_IP
```

Test the connection before making any further changes:

```bash
ssh sultan@YOUR_SERVER_IP
```

> [!IMPORTANT]
> Confirm key-based login works **before** disabling password authentication in the next step. Locking yourself out requires console access to recover.

---

### Harden SSH Configuration

The SSH daemon (`sshd`) controls how remote connections are accepted. By default it allows root login and password authentication — both should be disabled on any internet-facing server.

Open the SSH daemon config:

```bash
sudo nano /etc/ssh/sshd_config
```

Apply these settings:

```text {filename="/etc/ssh/sshd_config", hl_lines=[1,2,3]}
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart SSH to apply:

```bash
sudo systemctl restart ssh
```

> [!WARNING]
> Keep your current SSH session open while testing the new config in a second terminal. If the new session connects successfully, it is safe to close the original.

---

### Configure UFW Firewall

UFW (Uncomplicated Firewall) is a frontend for `iptables` — the Linux kernel's built-in packet filtering system. UFW simplifies firewall management with human-readable rules while providing the same underlying protection. By default it blocks all incoming traffic and allows all outgoing traffic.

Allow SSH before enabling the firewall — otherwise you will be locked out immediately:

```bash
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status
```

Expected output:

```text
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
```

Add additional rules as services are deployed:

```bash
sudo ufw allow 80/tcp    # HTTP
sudo ufw allow 443/tcp   # HTTPS
```

---

### Install and Configure Fail2ban

Fail2ban is an intrusion prevention tool that monitors system log files for repeated failed authentication attempts. When a source IP exceeds a configurable threshold, Fail2ban automatically adds a temporary firewall rule to block it. This is the standard lightweight defense against SSH brute-force attacks.

```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

Verify it is running and protecting SSH:

```bash
sudo systemctl status fail2ban
sudo fail2ban-client status sshd
```

{{< details title="Custom jail configuration (optional)" closed="true" >}}

A "jail" in Fail2ban is a set of rules that defines what to monitor, how many failures trigger a ban, and how long the ban lasts. The default SSH jail is active out of the box, but you can tune the thresholds.

Create a local override (never edit the default config directly — it gets overwritten on upgrades):

```bash
sudo nano /etc/fail2ban/jail.local
```

```ini {filename="/etc/fail2ban/jail.local"}
[DEFAULT]
bantime  = 1h
findtime = 10m
maxretry = 5

[sshd]
enabled = true
port    = ssh
logpath = %(sshd_log)s
backend = %(sshd_backend)s
```

Restart after changes:

```bash
sudo systemctl restart fail2ban
```

{{< /details >}}

---

### Enable Automatic Security Updates

`unattended-upgrades` is a package that automatically downloads and installs security patches from the Ubuntu security repository. It only applies security updates — it does not upgrade major packages or change system behavior. This ensures known vulnerabilities are patched without requiring manual intervention.

```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure unattended-upgrades
```

Select **Yes** when prompted.

Verify the configuration:

```bash
cat /etc/apt/apt.conf.d/20auto-upgrades
```

Expected:

```text
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

---

### Update System Packages

With security hardening in place, update the system to the latest available packages and install common utilities.

```bash
sudo apt update && sudo apt upgrade -y
```

Install essential utilities:

```bash
sudo apt install -y \
  curl \        # transfer data from URLs; used to fetch install scripts
  wget \        # download files from the web
  git \         # version control
  vim \         # terminal text editor
  unzip \       # extract .zip archives
  htop \        # interactive process viewer (better than top)
  net-tools \   # includes ifconfig, netstat, route
  ca-certificates \  # trusted TLS certificate authorities
  gnupg \       # GPG key management; required for adding apt repositories
  lsb-release   # prints Linux distribution info; used in install scripts
```

---

### Set Hostname and Timezone

**Hostname** identifies the server in logs, shell prompts, and network discovery. Set it to something meaningful.

```bash
sudo hostnamectl set-hostname your-hostname
```

Update `/etc/hosts` to match — this prevents sudo from producing "unable to resolve host" warnings:

```text {filename="/etc/hosts"}
127.0.0.1   localhost
127.0.1.1   your-hostname
```

**Timezone** affects log timestamps and scheduled jobs. Set it to your local timezone so logs are readable without mental conversion.

```bash
# List available timezones
timedatectl list-timezones | grep Asia

# Set timezone
sudo timedatectl set-timezone Asia/Jakarta

# Verify
timedatectl
```

---

### Install Docker

Docker is a container runtime that packages applications and their dependencies into isolated, portable units. It is the standard tool for running self-hosted services, CI/CD workloads, and local development environments on a VPS.

Use the official install script:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
```

Add your user to the `docker` group to run Docker without `sudo`:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Verify:

```bash
docker ps
docker run hello-world
```

{{% /steps %}}

---

## Optional: Custom MOTD

{{< details title="Add a custom Message of the Day" closed="true" >}}

MOTD (Message of the Day) is a script that runs on every SSH login and prints output to the terminal. It is purely cosmetic — a small quality-of-life addition for servers you log into regularly. Ubuntu runs all executable scripts in `/etc/update-motd.d/` on login and displays their output.

```bash
sudo nano /etc/update-motd.d/99-custom
```

```bash {filename="/etc/update-motd.d/99-custom"}
#!/bin/bash

echo ""
echo "======================================="
echo "   $(hostname) — Cloud Lab"
echo "======================================="
echo ""
echo " Uptime  : $(uptime -p)"
echo " Memory  : $(free -h | awk '/Mem:/ { print $3 "/" $2 }')"
echo " Disk    : $(df -h / | awk 'NR==2 {print $3 "/" $2}')"
echo " Load    : $(cut -d' ' -f1-3 /proc/loadavg)"
echo ""
```

Make it executable:

```bash
sudo chmod +x /etc/update-motd.d/99-custom
```

Reconnect via SSH to see it in action.

{{< /details >}}

---

## Verification

Run these checks after completing all steps to confirm the setup is correct.

{{< tabs >}}
{{< tab name="Services" >}}
```bash
systemctl status ssh
systemctl status fail2ban
systemctl status docker
systemctl status ufw
```
All four should show `active (running)`.
{{< /tab >}}
{{< tab name="Firewall" >}}
```bash
sudo ufw status verbose
```
Confirm only expected ports are open. At minimum: OpenSSH.
{{< /tab >}}
{{< tab name="SSH Config" >}}
```bash
sudo sshd -T | grep -E 'permitrootlogin|passwordauthentication|pubkeyauthentication'
```
Expected:
```text
permitrootlogin no
passwordauthentication no
pubkeyauthentication yes
```
{{< /tab >}}
{{< tab name="Fail2ban" >}}
```bash
sudo fail2ban-client status
sudo fail2ban-client status sshd
```
Confirm the `sshd` jail is active.
{{< /tab >}}
{{< /tabs >}}

---

## Hardening Checklist

- [x] Non-root sudo user created
- [x] SSH key authentication configured
- [x] Root SSH login disabled
- [x] Password authentication disabled
- [x] UFW firewall enabled
- [x] Fail2ban installed and active
- [x] Automatic security updates enabled
- [x] System packages updated
- [x] Hostname and timezone configured
- [x] Docker installed

---

## References

- [Ubuntu Server Guide — Security](https://ubuntu.com/server/docs/security-introduction)
- [UFW Documentation](https://help.ubuntu.com/community/UFW)
- [Fail2ban Documentation](https://www.fail2ban.org/wiki/index.php/Main_Page)
- [Docker Install — Official](https://docs.docker.com/engine/install/ubuntu/)
