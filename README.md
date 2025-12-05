<div align="center">

# Ahab Module: Docker

![Ahab Logo](https://raw.githubusercontent.com/waltdundore/ansible-control/prod/docs/images/ahab-logo.png)

**Docker Engine Installation Module**

*Docker CE, Docker Compose, and container management*

</div>

---

## Overview

The Docker module provides automated installation and configuration of Docker Engine on multiple Linux distributions. It handles repository setup, package installation, user permissions, and service configuration.

## Features

- ✅ Docker CE (Community Edition) installation
- ✅ Docker Compose Plugin
- ✅ Docker Buildx Plugin
- ✅ Containerd runtime
- ✅ User group management
- ✅ Service auto-start configuration
- ✅ Multi-distribution support (Fedora, Debian, Ubuntu, Raspberry Pi OS)
- ✅ Official Docker repositories or distribution packages

## Installation

### Using Ahab Module System

```bash
cd ~/git/ansible-control
make module-add
# Git URL: git@github.com:waltdundore/ahab-module-docker.git
# Module name: docker
```

### Manual Installation

```bash
cd ~/git/ansible-config/modules
git clone git@github.com:waltdundore/ahab-module-docker.git docker
```

## Configuration

Edit `config.yml` in this module to customize settings:

```yaml
---
# ==============================================================================
# Ahab Module: Docker Configuration
# ==============================================================================

docker:
  # Use official Docker repositories (recommended)
  # Set to false to use distribution-provided packages
  use_official_repo: true
  
  # Users to add to docker group (allows running docker without sudo)
  users:
    - vagrant
    - your-username
```

## Usage

### Standalone Playbook

```bash
cd ~/git/ansible-config/modules/docker
ansible-playbook -i ~/git/ansible-inventory/dev/hosts.yml playbooks/docker.yml
```

### Include in Your Playbook

```yaml
---
- name: Deploy with Docker
  hosts: all
  become: true
  
  roles:
    - role: ~/git/ansible-config/modules/docker/roles/docker
```

### Using Ahab Control

The module integrates automatically when installed in `ansible-config/modules/`:

```bash
cd ~/git/ansible-control
make deploy
```

## What It Does

### Repository Setup
- Adds official Docker GPG keys
- Configures Docker CE repositories
- Removes conflicting packages (moby-engine on Fedora, docker.io on Debian if using official repo)

### Package Installation
- **RedHat Family (Fedora):**
  - docker-ce
  - docker-ce-cli
  - containerd.io
  - docker-buildx-plugin
  - docker-compose-plugin

- **Debian Family (Debian/Ubuntu):**
  - docker-ce (if using official repo)
  - docker.io (if using distribution repo)
  - docker-compose-plugin
  - containerd
  - runc

### Service Configuration
- Starts Docker daemon
- Enables Docker to start on boot
- Adds specified users to docker group
- Verifies Docker installation

### User Permissions
- Adds users to docker group for non-root access
- Requires logout/login for group changes to take effect

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
- **Recommended:** ahab-module-common (for base system configuration)

## Module Structure

```
ahab-module-docker/
├── LICENSE                 # CC BY-NC 4.0
├── README.md              # This file
├── config.yml             # Module configuration
├── roles/
│   └── docker/
│       ├── defaults/
│       │   └── main.yml   # Default variables
│       ├── tasks/
│       │   ├── main.yml   # Main task orchestration
│       │   └── docker.yml # Docker installation
│       └── handlers/
│           └── main.yml
└── playbooks/
    └── docker.yml         # Standalone playbook
```

## Customization

### Adding Users to Docker Group

Edit `config.yml`:

```yaml
docker:
  users:
    - alice
    - bob
    - charlie
```

### Using Distribution Packages

Edit `config.yml`:

```yaml
docker:
  use_official_repo: false
```

**Note:** Distribution packages may be older versions. Official repositories provide the latest stable releases.

## Post-Installation

### Verify Installation

```bash
docker --version
docker compose version
docker run hello-world
```

### User Group Changes

Users added to the docker group must log out and back in for changes to take effect:

```bash
# Check current groups
groups

# After logout/login, docker should appear
groups
```

### Test Docker

```bash
docker run hello-world
docker ps
docker images
```

## Troubleshooting

### Docker Command Not Found

Ensure Docker service is running:
```bash
sudo systemctl status docker
sudo systemctl start docker
```

### Permission Denied

User not in docker group or hasn't logged out/in:
```bash
# Check group membership
groups

# Add user manually if needed
sudo usermod -aG docker $USER

# Log out and back in
```

### Repository Issues

Clear package cache and retry:
```bash
# RedHat
sudo dnf clean all && sudo dnf makecache

# Debian
sudo apt update
```

### Conflicting Packages

The module automatically removes conflicting packages, but if issues persist:
```bash
# RedHat
sudo dnf remove moby-engine moby-cli

# Debian
sudo apt remove docker docker-engine docker.io containerd runc
```

## Dependencies

- **Recommended:** ahab-module-common (provides base system configuration)
- **Optional:** ahab-module-monitoring (for container monitoring)

## Related Modules

- **ahab-module-common** - Base system configuration
- **ahab-module-monitoring** - Prometheus/Grafana for Docker monitoring
- **ahab-module-compose-stacks** - Pre-configured Docker Compose stacks

## Security Considerations

### Docker Group Access

Users in the docker group have root-equivalent access. Only add trusted users:

```yaml
docker:
  users:
    - trusted-user-only
```

### Network Security

Docker creates iptables rules. Ensure your firewall configuration is compatible.

### Image Security

Always use official images and scan for vulnerabilities:
```bash
docker scan image-name
```

## Contributing

This module is part of the Ahab project. To contribute:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test on all supported distributions
5. Submit a pull request

## Support

- GitHub Issues: https://github.com/waltdundore/ahab-module-docker/issues
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
