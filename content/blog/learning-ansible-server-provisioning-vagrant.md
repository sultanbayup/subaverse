---
title: 'Learning Ansible: Automating Server Provisioning with Vagrant'
date: '2025-05-19'
draft: false
tags: ['ansible', 'automation', 'linux', 'devops', 'vagrant']
summary: "My first hands-on experience with Ansible — provisioning two local VMs with Nginx, Docker, user management, and firewall config using playbooks and Vagrant."
featured_image: '/images/blog/learning-ansible-server-provisioning-vagrant/ansible.png'
---

I'd been reading about Ansible for a while — configuration management, idempotent playbooks, agentless architecture. At some point reading stops being useful and you just have to build something. So I set up a local lab with Vagrant and wrote my first real Ansible automation.

This is what I built, what I learned, and what I'd do differently.

## The Setup

Two Debian VMs running locally via Vagrant and VirtualBox:

- **web** — Nginx web server with UFW firewall rules
- **docker** — Docker Engine with a dedicated user and SSH key

Nothing production-scale, but enough to cover the core Ansible concepts: inventory, playbooks, roles, idempotency, and task sequencing.

The repo is at [github.com/sultanbayup/ansible-training](https://github.com/sultanbayup/ansible-training) if you want to follow along.

## Project Structure

```
ansible-training/
├── inventory/
│   └── hosts.ini              # Host groups: [web] and [docker]
├── playbooks/
│   ├── 01-nginx-install.yml   # Install and configure Nginx
│   ├── 02-docker-setup.yml    # Install Docker Engine
│   ├── 03-user-create.yml     # Create user + deploy SSH key
│   ├── 04-firewall-config.yml # Configure UFW rules
│   └── 05-status-check.yml    # Verify services are running
└── vagrant/
    └── Vagrantfile            # Two-VM local environment
```

The numbered playbooks were intentional — I wanted to run them in sequence and understand what each one did before moving to the next.

## What Each Playbook Does

### 01 — Nginx Install

Installs Nginx on the `web` group, ensures the service is started and enabled on boot. Simple, but this is where I first understood what idempotency actually means in practice — running the playbook twice doesn't reinstall Nginx, it just confirms the desired state is already met.

### 02 — Docker Setup

Installs Docker Engine on the `docker` group, including the GPG key, apt repository, and all dependencies. This one taught me about `apt_key` and `apt_repository` modules — things I'd normally do manually with a shell script, now declarative.

### 03 — User Create

Creates a new user and deploys a public SSH key from the `files/` directory. The `authorized_key` module handles the key deployment cleanly. No manual `~/.ssh/authorized_keys` editing.

### 04 — Firewall Config

Configures UFW on the web server — allows SSH and HTTP, denies everything else. The `ufw` module makes this readable. The equivalent shell commands are fine, but the playbook version is self-documenting.

### 05 — Status Check

Runs service status checks and prints the output. Useful for verifying the previous playbooks actually worked. Not something you'd keep in production automation, but helpful during learning.

## What I Actually Learned

**Idempotency is the point.** The first time I ran a playbook and saw `changed=0` on the second run, it clicked. Ansible isn't a script runner — it's a state enforcer. You describe what you want, not what to do.

**Inventory is more powerful than it looks.** `hosts.ini` seems trivial at first, but grouping hosts and using group variables is where Ansible's real flexibility comes from. I only scratched the surface here.

**Modules beat shell commands.** I was tempted to use `shell:` for everything because I already knew bash. But using proper modules (`apt`, `ufw`, `user`, `authorized_key`) gives you idempotency, better error messages, and readable output for free.

**Vagrant is a great Ansible lab.** Spinning up and destroying VMs with `vagrant up` and `vagrant destroy` made it easy to test from a clean state repeatedly. Much faster feedback loop than using real cloud VMs.

**Task ordering matters.** Running Docker setup before the user exists, or firewall config before Nginx is installed, causes failures. The numbered playbook approach was a crutch — in a real project you'd handle this with dependencies and roles.

## What I'd Do Differently

**Use roles instead of flat playbooks.** Roles give you a proper structure for reusable automation. Flat playbooks work for learning but don't scale. My next step would be refactoring each playbook into a role.

**Add variables.** I hardcoded values like usernames and package versions directly in tasks. Moving those to `group_vars/` or `host_vars/` would make the playbooks reusable across environments.

**Use `ansible-lint`.** I only discovered this after finishing. Running the linter would have caught several style issues and taught me best practices earlier.

**Write a single entry-point playbook.** Instead of running five playbooks manually in order, a `site.yml` that imports all of them in sequence would be cleaner and less error-prone.

## Final Thoughts

Ansible clicked for me faster than I expected. The learning curve is shallow compared to something like Terraform — if you know Linux and YAML, you can be productive quickly.

The gap between "I understand Ansible" and "I can write production-grade Ansible" is mostly about roles, variable management, and testing. That's where I'm headed next.

If you're just starting out, a Vagrant-based lab like this is the right way to begin. Break things, fix them, run the playbook again, see `changed=0`. That's the moment it makes sense.
