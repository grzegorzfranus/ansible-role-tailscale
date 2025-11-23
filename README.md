# Ansible Role: Tailscale

|Source|Version|Tests|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-tailscale)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-tailscale)](https://github.com/grzegorzfranus/ansible-role-tailscale/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-tailscale/actions/workflows/test-and-validation.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-tailscale/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

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
- 📁 **Dedicated Logging**: Optional separate log files with rsyslog integration and logrotate
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
List of officially supported operating systems for this role:
| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

### Ansible version

Ansible >= 2.15

**Compatibility Notes**:
- Fully tested with Ansible 2.15 through 2.20+
- Version 1.2.2+ includes compatibility fixes for Ansible 2.20's stricter variable recursion detection

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

### Logging Configuration Example

Enable dedicated logging to separate Tailscale logs from system logs:

```yaml
---
- name: Tailscale with Dedicated Logging
  hosts: all
  become: true
  vars:
    # Basic authentication
    tailscale_auth_key: "{{ vault_tailscale_auth_key }}"
    
    # Enable dedicated logging
    tailscale_enable_logging: true
    tailscale_log_dir: "/var/log/tailscale"
    tailscale_log_file: "/var/log/tailscale/tailscale.log"
    
    # Customize logrotate settings
    tailscale_logrotate_options:
      enabled: true
      frequency: "daily"
      rotate_count: 7
      compress: true
      notifempty: true
      copytruncate: true
      dateext: true
      dateformat: ".%Y-%m-%d"
      olddir: "/var/log/tailscale/archive"  # Automatically created if specified
  
  roles:
    - role: grzegorzfranus.tailscale
```

With this configuration, Tailscale logs will be directed to `/var/log/tailscale/tailscale.log` instead of appearing in the system syslog, making monitoring and troubleshooting much easier.

## 📊 Variables

### Authentication Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_auth_key` | Authentication key for connecting to Tailscale network. Generate from the [Tailscale admin console](https://login.tailscale.com/admin/authkeys). | `""` |
| `tailscale_extra_args` | Additional arguments for the `tailscale up` command. Supports multiple flags separated by spaces (e.g., `--hostname=server --accept-routes --accept-dns=false`). Useful for configuring hostname, enabling exit nodes, subnet routes, etc. | `""` |

### Installation Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_track` | Release track selection: `stable` (production-ready) or `unstable` (pre-release with latest features) | `"stable"` |
| `tailscale_prerequisites` | List of prerequisite packages needed before installing Tailscale | `["curl", "gnupg"]` |
| `tailscale_apt_key_type` | GPG key management method. This role uses the modern keyring method; legacy `apt-key` is not supported. | `"auto"` |
| `tailscale_install_method` | Installation method preference: `package` (recommended) or `binary` (future enhancement) | `"package"` |

### Service State and Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_service_enabled` | Whether the Tailscale service starts automatically on system boot | `true` |
| `tailscale_systemctl_start` | Service startup behavior: `auto` (detect if immediate start needed), `true` (always start), `false` (never start immediately) | `"auto"` |
| `tailscale_service_manager` | Service manager detection: `auto` (detect) or `systemd` (force systemd). OpenRC is not supported by this role. | `"auto"` |
| `tailscale_state` | Overall role behavior: `present` to install, `absent` to uninstall | `"present"` |

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

### Logging Configuration (Optional)

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_enable_logging` | Enable dedicated logging for Tailscale service (creates separate log files and configures rsyslog integration) | `false` |
| `tailscale_log_dir` | Directory for Tailscale log files and archives | `"/var/log/tailscale"` |
| `tailscale_log_file` | Main Tailscale log file path | `"{{ tailscale_log_dir }}/tailscale.log"` |
| `tailscale_log_file_permissions` | File permissions for log files (readable by system users) | `"0644"` |
| `tailscale_log_dir_permissions` | Directory permissions for log directory | `"0755"` |
| `tailscale_log_user` | Log file ownership user | `"syslog"` |
| `tailscale_log_group` | Log file ownership group | `"adm"` |
| `tailscale_syslog_identifier` | Syslog program identifier for filtering | `"tailscaled"` |
| `tailscale_rsyslog_config_file` | RSyslog configuration file name | `"49-tailscale.conf"` |

#### Logrotate Configuration Dictionary

| Variable | Description | Default |
|----------|-------------|---------|
| `tailscale_logrotate_options.enabled` | Enable logrotate configuration for Tailscale logs | `true` |
| `tailscale_logrotate_options.frequency` | Log rotation frequency (hourly, daily, weekly, monthly) | `"weekly"` |
| `tailscale_logrotate_options.rotate_count` | Number of rotated log files to keep | `4` |
| `tailscale_logrotate_options.compress` | Compress rotated log files using gzip | `true` |
| `tailscale_logrotate_options.notifempty` | Only rotate if log file is not empty | `true` |
| `tailscale_logrotate_options.copytruncate` | Use copytruncate instead of moving files (useful for busy log files) | `false` |
| `tailscale_logrotate_options.dateext` | Use date extension for rotated files instead of numbers | `true` |
| `tailscale_logrotate_options.dateformat` | Date format for rotated log files (requires dateext) | `".%Y-%m-%d"` |
| `tailscale_logrotate_options.olddir` | Directory to move old log files to (automatically created if specified, empty = same directory) | `""` |

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

### Uninstall

To remove Tailscale and its repository/configuration from a host:

```yaml
---
- name: Uninstall Tailscale
  hosts: all
  become: true
  roles:
    - role: grzegorzfranus.tailscale
      vars:
        tailscale_state: absent
```

## 🔒 Security considerations

- Use Ansible Vault for `tailscale_auth_key` and any secrets.
- Secrets masking: the role masks `--authkey` in `tailscale_extra_args` within error reports.
- The `tailscale up` task uses `no_log: true` to prevent secret leakage.

## 🧪 Check mode behavior

- Most informational tasks run in check mode.
- Mutating commands (auth, apt cache updates) are skipped in check mode where appropriate.
- Authentication is idempotent and skipped if the node is already authenticated.

## 🏷️ Tags usage

- Use `--tags` to run selective parts: `validate`, `repositories`, `install`, `service`, `logging`, `auth`.

## 🌐 Network resilience

- Network operations use timeouts and retries. Consider setting HTTP proxy env vars if needed.

## 🧰 Repository management

- Debian-family systems are configured via `apt_repository` with `signed-by` pointing to `{{ tailscale_repo_key_path }}`.
- The role uses the modern keyring method exclusively (no legacy `apt-key`).

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
grep -R "pkgs.tailscale.com" /etc/apt/sources.list.d/

# Check signed-by key file (keyring method)
ls -l /usr/share/keyrings/tailscale-archive-keyring.gpg

# Manual package refresh
sudo apt update
sudo apt install tailscale
```

### Ansible 2.20+ Compatibility

**Issue**: Recursive loop error with Ansible 2.20+

If you encounter the error `Recursive loop detected in template: maximum recursion depth exceeded` when using Ansible 2.20 or newer, this is caused by circular variable references.

**❌ Incorrect Usage (causes recursion):**
```yaml
- hosts: all
  vars:
    tailscale_auth_key: "your-auth-key"
  tasks:
    - ansible.builtin.include_role:
        name: ansible-role-tailscale
      vars:
        tailscale_auth_key: "{{ tailscale_auth_key }}"  # ❌ Circular reference!
```

**✅ Correct Usage (fixed in v1.2.2):**
```yaml
- hosts: all
  vars:
    tailscale_auth_key: "your-auth-key"
  tasks:
    - ansible.builtin.include_role:
        name: ansible-role-tailscale
      # No need to redefine tailscale_auth_key - it inherits from play vars
```

**Alternative: Define variables only at role level**
```yaml
- hosts: all
  tasks:
    - ansible.builtin.include_role:
        name: ansible-role-tailscale
      vars:
        tailscale_auth_key: "your-auth-key"
        tailscale_extra_args: "--accept-routes"
```

**Best Practice**: Avoid redefining the same variable at multiple scopes. Variables defined at play level are automatically inherited by included roles.

**Version Compatibility**:
- ✅ Ansible 2.15 - 2.19: Works with both patterns
- ✅ Ansible 2.20+: Requires correct pattern (fixed in role v1.2.2)

## 📁 File Structure

```
ansible-role-tailscale/
├── .github/                  # GitHub Actions workflows
│   └── workflows/           # CI/CD automation
│       └── test-and-validation.yml # 🧪 Testing and validation workflow
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
│   ├── logging.yml          # Dedicated logging configuration
│   └── error_reporting.yml  # Enhanced error reporting
├── templates/               # Configuration templates
│   ├── rsyslog/            # RSyslog configuration files
│   │   └── tailscale.conf.j2 # Tailscale rsyslog configuration
│   └── logrotate/          # Logrotate configuration files
│       └── tailscale.j2    # Tailscale log rotation settings
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
- `requirements` - Prerequisite and repository configuration tasks
- `install` - Package installation tasks
- `configure` - Service and role configuration tasks
- `logrotate` - Logrotate-specific configuration tasks

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

# Example: Tailscale with Dedicated Logging
- name: Configure Tailscale with Enhanced Logging
  hosts: servers
  become: true
  vars:
    tailscale_auth_key: "{{ vault_tailscale_auth_key }}"
    
    # Enable dedicated logging
    tailscale_enable_logging: true
    tailscale_log_dir: "/var/log/tailscale"
    
    # Logrotate configuration
    tailscale_logrotate_options:
      enabled: true
      frequency: "daily"
      rotate_count: 30
      compress: true
      copytruncate: true
      dateext: true
      dateformat: ".%Y-%m-%d"
    
    # Enhanced configuration
    tailscale_extra_args: >-
      --hostname={{ inventory_hostname }}-server
      --accept-routes
      --accept-dns
  
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