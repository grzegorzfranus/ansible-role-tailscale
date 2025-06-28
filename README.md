# Ansible Role: Tailscale

|Source|Version|Tests|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-tailscale)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-tailscale)](https://github.com/grzegorzfranus/ansible-role-tailscale/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-tailscale/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-tailscale/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs and configures Tailscale, a modern VPN service that creates secure point-to-point connections between devices. It provides zero-configuration networking with end-to-end encryption, automatic key rotation, and seamless cross-platform connectivity.

## ✨ Features

- 🌐 **Zero-Config VPN**: Automatic mesh networking without complex configuration
- 🔐 **End-to-End Encryption**: WireGuard-based encryption with automatic key rotation
- 🚀 **Multi-Platform Support**: Connect Linux, macOS, Windows, iOS, and Android devices
- 🛡️ **Smart Repository Management**: Automatic detection of optimal package sources
- 🔄 **Release Track Support**: Choose between stable and unstable release channels
- 📊 **Comprehensive Validation**: System compatibility checks and network connectivity tests
- 🧪 **Enhanced Error Reporting**: Detailed diagnostics for troubleshooting
- 🔧 **Flexible Service Control**: Configurable startup behavior and service management
- 📝 **Advanced Configuration**: Support for exit nodes, subnet routes, and custom hostnames
- 🧪 **Container Testing**: Full Molecule test suite for CI/CD integration

## 🎯 Architecture

Tailscale creates a secure mesh network (called a "tailnet") where each device can directly communicate with others:

```
Internet ←→ Tailscale Coordination Server
              ↕
Device A ←→ Device B ←→ Device C
  (mesh network with direct P2P connections)
```

### Network Topology Options

- **Standard Node**: Regular device in the tailnet
- **Exit Node**: Routes internet traffic for other devices
- **Subnet Router**: Provides access to local network subnets
- **Relay Node**: DERP relay for NAT traversal when P2P fails

## 📋 Requirements

- **Ansible**: 2.15 or higher
- **Network**: Internet access for Tailscale coordination server
- **Privileges**: sudo/root access on target hosts
- **Account**: Active Tailscale account with auth keys

### Supported operating systems
List of officially supported operating systems:
| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

### Ansible version

Ansible >= 2.15

### Python version

Python >= 3.9

### Setup module
The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, the role will not work properly.

### Root access
This role requires root access for package installation and service management. Make sure you are using a user with root privileges.

## 🚀 Quick Start

### 1. Basic Tailscale Installation

```yaml
---
- name: Install Tailscale
  hosts: all
  become: true
  roles:
    - role: grzegorzfranus.tailscale
      vars:
        tailscale_auth_key: "YOUR-AUTH-KEY-HERE"
```

### 2. Advanced Configuration with Exit Node

```yaml
---
- name: Configure Tailscale Exit Node
  hosts: exit_nodes
  become: true
  roles:
    - role: grzegorzfranus.tailscale
      vars:
        tailscale_auth_key: "{{ vault_tailscale_auth_key }}"
        tailscale_extra_args: "--advertise-exit-node --accept-routes --accept-dns"
        tailscale_track: "stable"
```

### 3. Run the playbook

```bash
ansible-playbook -i inventory tailscale-setup.yml
```

## ⚙️ Configuration

### Default Configuration

The role comes with production-ready defaults:

```yaml
# Authentication (must be set)
tailscale_auth_key: ""

# Service management
tailscale_service_enabled: true
tailscale_track: "stable"

# Validation and diagnostics
tailscale_validate_system: true
tailscale_enhanced_error_reporting: true
```

### Advanced Configuration

Customize for specific requirements:

```yaml
---
- name: Advanced Tailscale Configuration
  hosts: all
  become: true
  vars:
    # Authentication and connection settings
    tailscale_auth_key: "{{ vault_tailscale_auth_key }}"
    tailscale_extra_args: >-
      --hostname={{ inventory_hostname }}-{{ environment }}
      --accept-routes
      --accept-dns
      --ssh
    
    # Release and installation options
    tailscale_track: "stable"
    tailscale_apt_key_type: "auto"
    
    # Enhanced diagnostics
    tailscale_enhanced_error_reporting: true
    tailscale_collect_system_info: true
    tailscale_include_hardware_info: true
  roles:
    - role: grzegorzfranus.tailscale
```

## 📊 Variables

### Authentication Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_auth_key` | Authentication key for connecting to Tailscale network. Generate from the [Tailscale admin console](https://login.tailscale.com/admin/authkeys). | `""` |
| `tailscale_extra_args` | Additional arguments for the `tailscale up` command. Useful for configuring hostname, enabling exit nodes, etc. | `""` |

### Installation Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_track` | Release track selection: `stable` (production-ready) or `unstable` (pre-release with latest features) | `"stable"` |
| `tailscale_prerequisites` | List of prerequisite packages needed before installing Tailscale | `["curl", "gnupg"]` |
| `tailscale_apt_key_type` | GPG key management method: `auto` (detect based on OS), `legacy` (apt-key), `keyring` (modern method) | `"auto"` |
| `tailscale_install_method` | Installation method preference: `package` (recommended) or `binary` (future enhancement) | `"package"` |

### Service Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_service_enabled` | Whether the Tailscale service starts automatically on system boot | `true` |
| `tailscale_systemctl_start` | Service startup behavior: `auto` (detect if immediate start needed), `true` (always start), `false` (never start immediately) | `"auto"` |
| `tailscale_service_manager` | Service manager detection: `auto` (detect), `systemd` (force systemd), `openrc` (force OpenRC) | `"auto"` |

### System Validation Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_validate_system` | Enable comprehensive system validation checks | `true` |
| `tailscale_validate_connectivity` | Enable network connectivity verification to Tailscale servers | `true` |
| `tailscale_validate_prerequisites` | Enable architecture and package manager validation | `true` |
| `tailscale_collect_system_info` | Collect detailed system information for error reporting | `true` |
| `tailscale_connectivity_timeout` | Timeout for network connectivity checks (seconds) | `10` |

### Error Reporting Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_enhanced_error_reporting` | Enable enhanced error reporting with system diagnostics | `true` |
| `tailscale_include_hardware_info` | Include system hardware information in error reports | `true` |
| `tailscale_include_network_info` | Include network configuration in error reports | `true` |
| `tailscale_include_package_info` | Include package manager state in error reports | `true` |
| `tailscale_error_log_lines` | Maximum lines of log output to include in error reports | `50` |

## 🔍 Verification

After deployment, verify Tailscale is working correctly:

### Check Tailscale Status

```bash
# Check service status
sudo systemctl status tailscaled

# View Tailscale status and connectivity
sudo tailscale status

# Check current IP addresses
sudo tailscale ip -4
sudo tailscale ip -6
```

### Verify Network Connectivity

```bash
# Test connection to another node
ping tailscale-ip-of-another-node

# Check routing table
sudo tailscale status --peers

# View active connections
sudo tailscale netcheck
```

### Authentication and Keys

```bash
# Check authentication status
sudo tailscale status

# View current configuration
sudo tailscale file cp --help

# Re-authenticate if needed
sudo tailscale up --authkey=YOUR-NEW-AUTH-KEY
```

## 🛡️ Security Features

- ✅ **WireGuard Encryption**: Industry-standard encryption with automatic key rotation
- ✅ **Zero Trust Architecture**: No implicit trust, every connection verified
- ✅ **Secure Key Management**: Automatic detection of optimal GPG key methods
- ✅ **Repository Validation**: Comprehensive verification of package sources
- ✅ **System Hardening**: Secure defaults and minimal attack surface
- ✅ **Network Segmentation**: Support for subnet routing and access controls

### Enhanced Security Configuration

```yaml
# Use pre-authorized keys with expiration
tailscale_auth_key: "YOUR-PREAUTH-KEY-HERE"

# Restrict network access
tailscale_extra_args: >-
  --accept-routes=false
  --accept-dns=false
  --shields-up

# Validate all security aspects
tailscale_validate_system: true
tailscale_validate_connectivity: true
```

## 🔧 Troubleshooting

### Service Issues

```bash
# Check if tailscaled is running
sudo systemctl status tailscaled

# View detailed logs
sudo journalctl -u tailscaled -f

# Restart service if needed
sudo systemctl restart tailscaled
```

### Authentication Problems

```bash
# Check current authentication status
sudo tailscale status

# Re-authenticate with new key
sudo tailscale up --authkey=YOUR-NEW-AUTH-KEY

# Check for expired keys
sudo tailscale status | grep -i expire
```

### Network Connectivity Issues

```bash
# Test DERP connectivity
sudo tailscale netcheck

# Check NAT traversal
sudo tailscale ping peer-hostname

# Verify routing
ip route show | grep tailscale
```

### Repository and Installation Issues

```bash
# Verify repository configuration
cat /etc/apt/sources.list.d/tailscale.list

# Check GPG key
apt-key list | grep -i tailscale

# Manual package refresh
sudo apt update
sudo apt install tailscale
```

## 📁 File Structure

```
ansible-role-tailscale/
├── .github/                  # GitHub Actions workflows
│   └── workflows/           # CI/CD automation
│       └── ci.yml           # 🧪 Testing and validation workflow
├── CHANGELOG.md              # Version history and changes
├── LICENSE                   # Apache-2.0 license
├── README.md                # This documentation file
├── defaults/
│   └── main.yml             # Default configuration variables
├── handlers/
│   └── main.yml             # Service restart and reload handlers
├── meta/
│   └── main.yml             # Role metadata and Galaxy information
├── molecule/                 # Molecule testing framework
│   └── default/             # Default test scenario
│       ├── molecule.yml     # Test configuration
│       ├── converge.yml     # Role execution playbook
│       ├── prepare.yml      # Test preparation tasks
│       └── verify.yml       # Verification tests
├── tasks/
│   ├── main.yml             # Main task orchestration
│   ├── assert.yml           # Variable validation and system checks
│   ├── prerequisites.yml    # System preparation
│   ├── install.yml          # Package installation
│   ├── repository.yml       # Repository configuration
│   ├── service.yml          # Service management
│   ├── validate.yml         # System validation and diagnostics
│   └── error_reporting.yml  # Enhanced error reporting
├── templates/               # Configuration templates (currently empty)
└── vars/
    ├── main.yml             # Internal role variables
    ├── debian_12.yml        # Debian 12 specific variables
    └── ubuntu_24.04.yml     # Ubuntu 24.04 specific variables
```

## 🏷️ Tags

- `always` - Tasks that always run (variable loading and validation)
- `setup` - Setup and configuration tasks
- `init` - Initial environment setup and variable loading
- `validate` - Variable validation and system checks
- `install` - Package installation tasks
- `packages` - Tasks related to installing prerequisite packages
- `repositories` - Repository configuration tasks
- `service` - Service startup configuration tasks
- `configure` - Tailscale configuration tasks
- `auth` - Authentication and network joining tasks

## Example Playbook

```yaml
---
- name: Configure Tailscale VPN Network
  hosts: all
  become: true
  vars:
    # Authentication (store securely with Ansible Vault)
    tailscale_auth_key: "{{ vault_tailscale_auth_key }}"
    
    # Advanced connection settings
    tailscale_extra_args: >-
      --hostname={{ inventory_hostname }}-{{ environment }}
      --accept-routes
      --accept-dns
      --ssh
      --shields-up
    
    # Installation preferences
    tailscale_track: "stable"
    tailscale_service_enabled: true
    
    # Enhanced validation and reporting
    tailscale_validate_system: true
    tailscale_validate_connectivity: true
    tailscale_enhanced_error_reporting: true
    tailscale_collect_system_info: true
  
  roles:
    - role: grzegorzfranus.tailscale

# Example: Configure Tailscale Exit Nodes
- name: Configure Tailscale Exit Nodes
  hosts: exit_nodes
  become: true
  vars:
    tailscale_auth_key: "{{ vault_tailscale_exit_node_key }}"
    tailscale_extra_args: >-
      --hostname={{ inventory_hostname }}-exit
      --advertise-exit-node
      --accept-routes
      --accept-dns
    tailscale_systemctl_start: true
  
  roles:
    - role: grzegorzfranus.tailscale

# Example: Configure Subnet Routers
- name: Configure Tailscale Subnet Routers
  hosts: subnet_routers
  become: true
  vars:
    tailscale_auth_key: "{{ vault_tailscale_subnet_key }}"
    tailscale_extra_args: >-
      --hostname={{ inventory_hostname }}-router
      --advertise-routes=192.168.1.0/24,10.0.0.0/8
      --accept-routes
      --accept-dns
  
  roles:
    - role: grzegorzfranus.tailscale

# Example: Client-only Configuration
- name: Configure Tailscale Clients
  hosts: workstations
  become: true
  vars:
    tailscale_auth_key: "{{ vault_tailscale_client_key }}"
    tailscale_extra_args: >-
      --hostname={{ ansible_hostname }}-{{ ansible_user }}
      --accept-routes
      --accept-dns
      --ssh
    tailscale_service_enabled: true
  
  roles:
    - role: grzegorzfranus.tailscale
```

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`.
- Make your changes with clear, descriptive commit messages.
- Ensure your code passes all Molecule and lint tests.
- Submit a pull request describing your changes and the motivation.
- For major changes, please open an issue first to discuss what you would like to change.

If you have questions or suggestions, feel free to open an issue or contact the author via GitHub.

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).
