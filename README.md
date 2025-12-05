<div align="center">

# Ahab Module: Common

![Ahab Logo](https://raw.githubusercontent.com/waltdundore/ansible-control/prod/docs/images/ahab-logo.png)

**Base System Configuration Module**

*SSH keys, time sync, packages, and user management*

</div>

---

## Overview

The Common module provides essential base system configuration for all Ahab-managed hosts. This is typically the first module you'll install and is required by most other modules.

## Features

- ✅ SSH public key deployment
- ✅ Time synchronization (chrony)
- ✅ Base package installation/removal
- ✅ User and sudo configuration
- ✅ SELinux configuration (RedHat)
- ✅ Firewall management
- ✅ Multi-distribution support (Fedora, Debian, Ubuntu, Raspberry Pi OS)

## Installation

### Using Ahab Module System

```bash
cd ~/git/ansible-control
make module-add
# Git URL: git@github.com:waltdundore/ahab-module-common.git
# Module name: common
```

### Manual Installation

```bash
cd ~/git/ansible-config/modules
git clone git@github.com:waltdundore/ahab-module-common.git common
```

## Configuration

Edit `config.yml` in this module to customize settings:

```yaml
---
# ==============================================================================
# Ahab Module: Common Configuration
# ==============================================================================

common:
  # User Configuration
  user: "{{ ansible_user }}"
  
  # SSH Configuration
  ssh_key: "~/.ssh/id_ed25519.pub"
  
  # Time Configuration
  timezone: "America/New_York"
  
  # Package Management
  packages_to_remove:
    - elisa
    - transmission
  
  packages_to_install:
    - git
    - htop
    - iperf3
    - rsync
    - screen
    - wget
```

## Usage

### Standalone Playbook

```bash
cd ~/git/ansible-config/modules/common
ansible-playbook -i ~/git/ansible-inventory/dev/hosts.yml playbooks/common.yml
```

### Include in Your Playbook

```yaml
---
- name: Deploy with common module
  hosts: all
  become: true
  
  roles:
    - role: ~/git/ansible-config/modules/common/roles/common
```

### Using Ahab Control

The module integrates automatically when installed in `ansible-config/modules/`:

```bash
cd ~/git/ansible-control
make deploy
```

## What It Does

### SSH Keys
- Deploys your SSH public key to target hosts
- Configures authorized_keys for the specified user
- Supports both vagrant and custom users

### Time Synchronization
- Installs and configures chrony
- Sets timezone (configurable)
- Ensures time service is running and enabled

### Package Management
- Removes unwanted packages (configurable list)
- Installs essential packages (configurable list)
- Supports both RedHat (dnf) and Debian (apt) families

### User Management
- Configures sudo access for specified user
- Creates sudoers file with proper validation
- Sets appropriate permissions

### System Configuration
- Installs SELinux Python bindings (RedHat)
- Stops and disables firewalld (RedHat)
- Prepares system for containerized workloads

## Supported Distributions

- ✅ Fedora 40+
- ✅ Debian 12 (Bookworm)
- ✅ Debian 13 (Trixie)
- ✅ Ubuntu 22.04 LTS
- ✅ Ubuntu 24.04 LTS
- ✅ Raspberry Pi OS (Debian-based)

## Requirements

- Ansible 2.9+
- Python 3.6+
- SSH access to target hosts
- Sudo privileges on target hosts

## Module Structure

```
ahab-module-common/
├── LICENSE                 # CC BY-NC 4.0
├── README.md              # This file
├── config.yml             # Module configuration
├── roles/
│   └── common/
│       ├── defaults/
│       │   └── main.yml   # Default variables
│       ├── tasks/
│       │   ├── main.yml   # Main task orchestration
│       │   ├── ssh_keys.yml
│       │   ├── chrony.yml
│       │   ├── prerequisites.yml
│       │   └── base_apps.yml
│       ├── templates/
│       │   ├── chrony.conf.j2
│       │   └── sudoers_user.j2
│       └── handlers/
│           └── main.yml
└── playbooks/
    └── common.yml         # Standalone playbook
```

## Customization

### Adding Packages

Edit `config.yml`:

```yaml
common:
  packages_to_install:
    - git
    - htop
    - your-package-here
```

### Changing Timezone

Edit `config.yml`:

```yaml
common:
  timezone: "Europe/London"
```

### Using Different SSH Key

Edit `config.yml`:

```yaml
common:
  ssh_key: "~/.ssh/id_rsa.pub"
```

## Troubleshooting

### SSH Key Not Deploying

Check that the key file exists:
```bash
ls -la ~/.ssh/id_ed25519.pub
```

### Time Sync Issues

Verify chrony is running:
```bash
systemctl status chronyd
```

### Package Installation Fails

Update package cache:
```bash
# RedHat
sudo dnf clean all && sudo dnf makecache

# Debian
sudo apt update
```

## Dependencies

None - this is a base module with no dependencies.

## Related Modules

- **ahab-module-docker** - Docker installation (requires common)
- **ahab-module-monitoring** - Prometheus/Grafana (requires common)

## Contributing

This module is part of the Ahab project. To contribute:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test on all supported distributions
5. Submit a pull request

## Support

- GitHub Issues: https://github.com/waltdundore/ahab-module-common/issues
- Website: https://ahabsoftware.com
- Documentation: https://github.com/waltdundore/ansible-control

## License

Licensed under Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)

See LICENSE file for details.

## About

**Ahab Software, LLC**  
Automated Host Administration & Build

Website: [ahabsoftware.com](https://ahabsoftware.com)  
GitHub: [github.com/waltdundore](https://github.com/waltdundore)

---

*Part of the Ahab infrastructure automation suite*
