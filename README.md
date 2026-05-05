# Ansible Playbooks

A collection of Ansible playbooks for managing Linux infrastructure. Organized by
category for use with [Ansible Semaphore](https://ansible-semaphore.com/).

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
| [`aptupdate.yml`](maintenance/aptupdate.yml) | Updates the APT package cache on all hosts (skips if done within 24h) |
| [`aptupgrade.yml`](maintenance/aptupgrade.yml) | Full `dist-upgrade` with autoremove in non-interactive mode |
| [`reboot.yml`](maintenance/reboot.yml) | Reboots all hosts and waits for them to come back online (1h timeout) |

### `setup/`

One-time or infrequent tasks for provisioning new hosts. These configure the
baseline state of a machine — installing standard packages, setting the timezone,
or installing hypervisor integration tools.

| Playbook | Description |
|----------|-------------|
| [`set-qol-packages.yml`](setup/set-qol-packages.yml) | Installs a standardized set of sysadmin & QoL packages (tmux, htop, nmap, etc.) |
| [`set-timezone.yml`](setup/set-timezone.yml) | Sets the system timezone and enables NTP sync (skips LXC containers) |
| [`qemu-guest-agent.yml`](setup/qemu-guest-agent.yml) | Installs and enables the QEMU guest agent for Proxmox integration |

### `security/`

Playbooks that enforce security policies or audit compliance. Covers SSH key
management, enabling automatic security updates, and checking that security
configurations are in place.

| Playbook | Description |
|----------|-------------|
| [`validate-keys.yml`](security/validate-keys.yml) | Deploys SSH public keys to root's `authorized_keys` |
| [`enforce-unattended-upgrades.yml`](security/enforce-unattended-upgrades.yml) | Installs, configures, and enables unattended-upgrades for automatic security patches |
| [`audit-unattended-upgrades.yml`](security/audit-unattended-upgrades.yml) | Audits whether unattended-upgrades is installed and enabled |

### `monitoring/`

Agents, health checks, and diagnostic tools. These deploy monitoring software or
gather system health information like disk usage.

| Playbook | Description |
|----------|-------------|
| [`diskspace.yml`](monitoring/diskspace.yml) | Reports disk usage across all hosts via `df -h` |
| [`patchmon-install-agent.yml`](monitoring/patchmon-install-agent.yml) | Deploys the Patchmon monitoring agent (requires env vars) |

### `connectivity/`

Playbooks for testing and validating network/SSH access to hosts. Used to confirm
inventory reachability and that Semaphore can pull playbooks from GitHub.

| Playbook | Description |
|----------|-------------|
| [`ping-lxcs.yml`](connectivity/ping-lxcs.yml) | Tests SSH connectivity to all LXC containers in the inventory |
| [`test-connection.yml`](connectivity/test-connection.yml) | Validates that Semaphore can pull and execute playbooks from GitHub |

### `examples/`

Reference playbooks that demonstrate Ansible features or patterns. Not intended
for production use — serves as a template library.

| Playbook | Description |
|----------|-------------|
| [`findandreplace-example.yml`](examples/findandreplace-example.yml) | Demonstrates the `replace` module to uncomment a line in a config file |

---

## Semaphore Playbook Paths

When creating tasks in Ansible Semaphore, use the following paths (relative to
the repository root):

| Task Name | Playbook Path |
|-----------|--------------|
| Update APT Cache | `maintenance/aptupdate.yml` |
| Full Dist-Upgrade | `maintenance/aptupgrade.yml` |
| Reboot Hosts | `maintenance/reboot.yml` |
| Install QoL Packages | `setup/set-qol-packages.yml` |
| Set Timezone | `setup/set-timezone.yml` |
| Install QEMU Guest Agent | `setup/qemu-guest-agent.yml` |
| Deploy SSH Keys | `security/validate-keys.yml` |
| Enforce Unattended Upgrades | `security/enforce-unattended-upgrades.yml` |
| Audit Unattended Upgrades | `security/audit-unattended-upgrades.yml` |
| Check Disk Space | `monitoring/diskspace.yml` |
| Deploy Patchmon Agent | `monitoring/patchmon-install-agent.yml` |
| Test LXC Connectivity | `connectivity/ping-lxcs.yml` |
| Verify Semaphore Access | `connectivity/test-connection.yml` |
| Find & Replace Example | `examples/findandreplace-example.yml` |

---

## Requirements

- **Ansible** >= 2.12
- **Collections:**
  - `ansible.posix` — used by `validate-keys.yml`
  - `community.general` — used by `set-timezone.yml`

Install collections with:
```bash
ansible-galaxy collection install ansible.posix community.general
```

## Environment Variables

The following environment variables are required by specific playbooks and should
be configured in Semaphore:

| Variable | Used By | Description |
|----------|---------|-------------|
| `PATCHMON_ID` | `patchmon-install-agent.yml` | Patchmon API ID |
| `PATCHMON_KEY` | `patchmon-install-agent.yml` | Patchmon API key |
| `PATCHMON_URL` | `patchmon-install-agent.yml` | Patchmon agent download URL |
| `my_root_key` | `validate-keys.yml` | SSH public key to deploy (inventory variable) |
