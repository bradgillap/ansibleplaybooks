# Ansible Playbooks

A collection of Ansible playbooks for managing Linux infrastructure. Organized by
category for use with [Ansible Semaphore](https://ansible-semaphore.com/).

## Naming Convention

All playbooks follow the `{os}-{verb}-{noun}.yml` pattern:

| Prefix | Meaning |
|--------|---------|
| `deb-` | Debian/Ubuntu specific (uses `apt` module) |
| `linux-` | Generic Linux (uses `package`, `systemd`, `command`, etc.) |
| `win-` | Windows (future) |

## Directory Structure

```
ansibleplaybooks/
├── maintenance/        # Routine scheduled maintenance tasks
├── setup/              # Initial host provisioning & configuration
├── security/           # Security hardening & compliance
├── monitoring/         # Monitoring agents & health checks
├── connectivity/       # Connection testing & validation
└── examples/           # Example/reference playbooks
```

---

## Categories

### `maintenance/`

Routine tasks that run on a schedule or during maintenance windows. These are the
"keep the lights on" playbooks — updating packages, upgrading systems, and
rebooting hosts.

| Playbook | Description |
|----------|-------------|
| [`deb-update-cache.yml`](maintenance/deb-update-cache.yml) | Updates the APT package cache on all hosts (skips if done within 24h) |
| [`deb-dist-upgrade.yml`](maintenance/deb-dist-upgrade.yml) | Full `dist-upgrade` with autoremove in non-interactive mode |
| [`linux-reboot.yml`](maintenance/linux-reboot.yml) | Reboots all hosts and waits for them to come back online (1h timeout) |

### `setup/`

One-time or infrequent tasks for provisioning new hosts. These configure the
baseline state of a machine — installing standard packages, setting the timezone,
or installing hypervisor integration tools.

| Playbook | Description |
|----------|-------------|
| [`deb-install-qol-packages.yml`](setup/deb-install-qol-packages.yml) | Installs a standardized set of sysadmin & QoL packages (tmux, htop, nmap, etc.) |
| [`linux-configure-timezone.yml`](setup/linux-configure-timezone.yml) | Sets the system timezone and enables NTP sync (skips LXC containers) |
| [`deb-install-qemu-guest-agent.yml`](setup/deb-install-qemu-guest-agent.yml) | Installs and enables the QEMU guest agent for Proxmox integration |

### `security/`

Playbooks that enforce security policies or audit compliance. Covers SSH key
management, enabling automatic security updates, and checking that security
configurations are in place.

| Playbook | Description |
|----------|-------------|
| [`linux-deploy-ssh-keys.yml`](security/linux-deploy-ssh-keys.yml) | Deploys SSH public keys to root's `authorized_keys` |
| [`deb-enforce-unattended-upgrades.yml`](security/deb-enforce-unattended-upgrades.yml) | Installs, configures, and enables unattended-upgrades for automatic security patches |
| [`deb-audit-unattended-upgrades.yml`](security/deb-audit-unattended-upgrades.yml) | Audits whether unattended-upgrades is installed and enabled |

### `monitoring/`

Agents, health checks, and diagnostic tools. These deploy monitoring software or
gather system health information like disk usage.

| Playbook | Description |
|----------|-------------|
| [`linux-check-disk-space.yml`](monitoring/linux-check-disk-space.yml) | Reports disk usage across all hosts via `df -h` |
| [`linux-install-patchmon-agent.yml`](monitoring/linux-install-patchmon-agent.yml) | Deploys the Patchmon monitoring agent (requires env vars) |

### `connectivity/`

Playbooks for testing and validating network/SSH access to hosts. Used to confirm
inventory reachability and that Semaphore can pull playbooks from GitHub.

| Playbook | Description |
|----------|-------------|
| [`linux-test-lxc-connectivity.yml`](connectivity/linux-test-lxc-connectivity.yml) | Tests SSH connectivity to all LXC containers in the inventory |
| [`linux-verify-semaphore-access.yml`](connectivity/linux-verify-semaphore-access.yml) | Validates that Semaphore can pull and execute playbooks from GitHub |

### `examples/`

Reference playbooks that demonstrate Ansible features or patterns. Not intended
for production use — serves as a template library.

| Playbook | Description |
|----------|-------------|
| [`linux-replace-module-example.yml`](examples/linux-replace-module-example.yml) | Demonstrates the `replace` module to uncomment a line in a config file |

---

## Semaphore Playbook Paths

When creating tasks in Ansible Semaphore, use the following paths (relative to
the repository root):

| Task Name | Playbook Path |
|-----------|--------------|
| Update APT Cache | `maintenance/deb-update-cache.yml` |
| Full Dist-Upgrade | `maintenance/deb-dist-upgrade.yml` |
| Reboot Hosts | `maintenance/linux-reboot.yml` |
| Install QoL Packages | `setup/deb-install-qol-packages.yml` |
| Configure Timezone | `setup/linux-configure-timezone.yml` |
| Install QEMU Guest Agent | `setup/deb-install-qemu-guest-agent.yml` |
| Deploy SSH Keys | `security/linux-deploy-ssh-keys.yml` |
| Enforce Unattended Upgrades | `security/deb-enforce-unattended-upgrades.yml` |
| Audit Unattended Upgrades | `security/deb-audit-unattended-upgrades.yml` |
| Check Disk Space | `monitoring/linux-check-disk-space.yml` |
| Install Patchmon Agent | `monitoring/linux-install-patchmon-agent.yml` |
| Test LXC Connectivity | `connectivity/linux-test-lxc-connectivity.yml` |
| Verify Semaphore Access | `connectivity/linux-verify-semaphore-access.yml` |
| Replace Module Example | `examples/linux-replace-module-example.yml` |

---

## Requirements

- **Ansible** >= 2.12
- **Collections:**
  - `ansible.posix` — used by `linux-deploy-ssh-keys.yml`
  - `community.general` — used by `linux-configure-timezone.yml`

Install collections with:
```bash
ansible-galaxy collection install ansible.posix community.general
```

## Environment Variables

The following environment variables are required by specific playbooks and should
be configured in Semaphore:

| Variable | Used By | Description |
|----------|---------|-------------|
| `PATCHMON_ID` | `linux-install-patchmon-agent.yml` | Patchmon API ID |
| `PATCHMON_KEY` | `linux-install-patchmon-agent.yml` | Patchmon API key |
| `PATCHMON_URL` | `linux-install-patchmon-agent.yml` | Patchmon agent download URL |
| `my_root_key` | `linux-deploy-ssh-keys.yml` | SSH public key to deploy (inventory variable) |
