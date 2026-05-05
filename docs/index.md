---
layout: default
title: Linux defaults with ansible
---

# Linux defaults with ansible

> A one-shot Ansible playbook that takes a fresh Debian or Ubuntu host and
> turns it into something you would actually want to leave on the open
> internet.

A mini-project by **Andro & Robin** for Tero Karvinen's *Palvelimien
Hallinta* course.

---

## What you get

Run one command and the playbook configures:

- **Hardened SSH** — no root login, key-only authentication, idle-client
  timeout.
- **UFW firewall** — default-deny incoming, port 22/tcp allowed, enabled at
  boot.
- **fail2ban** — bans an IP for 24 hours after 3 failed SSH attempts within
  10 minutes.
- **Unattended security updates** — daily package list refresh, automatic
  security patches, weekly cleanup.
- **A usable tmux** — mouse on, sane pane numbering, intuitive splits
  (`|` / `-`), Alt+Arrow window switching, status bar with host and clock.

## Quick start

```bash
sudo apt update
sudo apt install -y git ansible
git clone https://github.com/AndroOtt/Linux-defaults-with-ansible.git
cd Linux-defaults-with-ansible/ansible
sudo ansible-playbook site.yml
```

> **Before you run it:** SSH password authentication will be disabled. Make
> sure you have a working public key in `~/.ssh/authorized_keys` for any
> account you log in with remotely, or you will be locked out.

Want a dry run first?

```bash
sudo ansible-playbook site.yml --check --diff
```

## After the run

```bash
sudo ufw status
sudo fail2ban-client status sshd
sudo sshd -T | grep -E 'permitrootlogin|passwordauthentication'
tmux -V
```

## Full documentation

The complete docs — requirements, role-by-role breakdown, file layout, and
the project task split — live in the
[README on GitHub](https://github.com/AndroOtt/Linux-defaults-with-ansible#readme).

## Source

[github.com/AndroOtt/Linux-defaults-with-ansible](https://github.com/AndroOtt/Linux-defaults-with-ansible)
