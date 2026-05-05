# Linux defaults with ansible (how-it-should-be)

Andro & Robin's mini-project for Tero Karvinen's *Palvelimien Hallinta* course.

The goal is a one-shot Ansible playbook that takes a fresh Debian/Ubuntu host
and applies a sensible set of defaults: a hardened SSH server, a working
firewall, automatic security updates, brute-force protection, and a usable
tmux configuration.

## What it does

Running the playbook applies two roles to the local machine:

- **`default_security`** — installs and configures `fail2ban`, `ufw`,
  `unattended-upgrades`, and `openssh-server`. Default UFW policy is set to
  deny incoming traffic, port 22/tcp is allowed, and the firewall is enabled.
  SSH is hardened (no root login, no password auth, keys only, idle-client
  timeout). APT is configured to download and apply security updates
  automatically.
- **`tmux`** — installs `tmux` and drops a system-wide `tmux.conf` with
  mouse support, sane pane numbering, intuitive split bindings (`|` / `-`),
  Alt+Arrow window switching, and a status bar showing host and clock.

## Requirements

- Debian or Ubuntu host (tested on recent releases).
- `sudo` privileges (the playbook uses `become: true`).
- `git` and `ansible` (installed in step 1 below).

## Installation

### 1. Install Ansible and Git

On Debian / Ubuntu:

```bash
sudo apt update
sudo apt install -y git ansible
```

Verify the install:

```bash
ansible --version
```

### 2. Make sure you have an SSH key (important)

The playbook hardens `sshd` so that **password authentication is
disabled**. Before you run it, confirm you can log in with a key — or you
risk locking yourself out of remote sessions.

Generate a key if you do not have one yet, and add the public key to
`~/.ssh/authorized_keys` for every account that needs remote access:

```bash
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### 3. Clone the repository

```bash
git clone https://github.com/<owner>/Linux-defaults-with-ansible.git
cd Linux-defaults-with-ansible/ansible
```

### 4. Run the playbook

`site.yml` targets `localhost` with a local connection, so no inventory or
remote SSH setup is required:

```bash
sudo ansible-playbook site.yml
```

To preview the changes without applying them, run a dry run first:

```bash
sudo ansible-playbook site.yml --check --diff
```

### 5. Verify

After the run completes you can confirm the most important pieces:

```bash
sudo ufw status            # firewall enabled, 22/tcp allowed
sudo systemctl status fail2ban
sudo fail2ban-client status sshd
sudo sshd -T | grep -E 'permitrootlogin|passwordauthentication'
tmux -V
```

## Repository layout

```
ansible/
├── site.yml                              # top-level playbook
└── roles/
    ├── default_security/
    │   ├── files/
    │   │   ├── 20auto-upgrades           # APT periodic settings
    │   │   ├── 50unattended-upgrades     # unattended-upgrades policy
    │   │   ├── 99-hardened.conf          # sshd_config drop-in
    │   │   └── jail.local                # fail2ban sshd jail
    │   ├── handlers/main.yml             # restart fail2ban / ssh
    │   └── roles/main.yml                # tasks for the role
    └── tmux/
        ├── files/tmux.conf               # system-wide tmux config
        └── tasks/main.yml                # install + deploy config
```

## Roles

### `default_security`

| Component            | Configuration source                    | Effect                                                                 |
| -------------------- | --------------------------------------- | ---------------------------------------------------------------------- |
| `fail2ban`           | `files/jail.local`                      | sshd jail, 24 h ban after 3 failed attempts within 10 minutes          |
| `ufw`                | inline commands                         | default-deny incoming, 22/tcp allowed, firewall enabled                |
| `openssh-server`     | `files/99-hardened.conf`                | no root login, key-only auth, 5-minute idle timeout (max 2 missed)     |
| `unattended-upgrades`| `files/50unattended-upgrades`, `20auto-upgrades` | daily list update, automatic security updates, weekly autoclean |

Handlers restart `fail2ban` and `openssh-server` when their configuration
changes.

### `tmux`

Installs `tmux` from the distribution repositories and copies
`files/tmux.conf` to `/etc/tmux.conf`, making the configuration available to
all users on the host.

## Project split

- Tmux automation and project homepage — Andro
- UFW / firewall, automatic updates, fail2ban + SSH — Robin

## License

See [`LICENSE`](LICENSE).
