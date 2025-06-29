# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2025-06-29

### Added ✅
- 📁 **Optional Dedicated Logging Configuration** - New comprehensive logging system for Tailscale
  - `tailscale_enable_logging` flag to control separate log files (disabled by default)
  - Creates `/var/log/tailscale/tailscale.log` for Tailscale-specific logs separated from system syslog
  - RSyslog integration with automatic service configuration and log filtering
  - Systemd service override for proper syslog identifier configuration
  - Advanced logrotate configuration with dictionary structure for easy management
  - Support for copytruncate, dateext, custom date formats, and archive directories
  - New `logging` tag for selective task execution

### Changed 🔄
- **Logrotate Configuration Structure** - Restructured individual variables into organized dictionary
  - `tailscale_logrotate_options` dictionary replaces 9+ individual variables
  - Cleaner, more maintainable configuration similar to chrony role pattern
  - Inline comments for better variable documentation
  - Removed `maxsize` option for simplified time-based rotation
  - Made `notifempty` optional with sensible default (enabled)

### Added Files 📁
- `tasks/logging.yml` - Complete logging configuration tasks
- `templates/rsyslog/tailscale.conf.j2` - RSyslog integration template
- `templates/logrotate/tailscale.j2` - Advanced logrotate configuration template

### Updated Documentation 📝
- Added comprehensive logging configuration section in README.md
- New "Logrotate Configuration Dictionary" section with all options documented
- Multiple configuration examples showing logging capabilities
- Updated file structure documentation to reflect new templates
- Added `logging` tag to tags documentation

### Quality Assurance ✅
- All files pass yamllint and ansible-lint production standards
- Comprehensive testing with 18 files validated
- Backward compatibility maintained (logging disabled by default)
- Professional-grade implementation with error handling and status reporting

## [1.0.0] - 2025-06-28

### Added ✅
- 🚀 **Initial release** of Ansible role for Tailscale VPN installation and configuration
- 🌐 **Multi-platform support** for Ubuntu 24.04/22.04 and Debian 12/11
- 🔑 **Smart GPG key management** with automatic detection of optimal method (legacy vs keyring)
- 🎯 **Release track support** for stable and unstable Tailscale releases
- 📦 **Automated package installation** with secure repository configuration
- 🔧 **Intelligent service management** with systemd and OpenRC support
- 🔍 **Comprehensive system validation** including architecture, connectivity, and prerequisites
- 📊 **Enhanced error reporting** with detailed diagnostics and troubleshooting data
- 🛡️ **Security-focused configuration** with GPG verification and secure defaults
- ⚙️ **Flexible configuration options** with 15+ customizable variables
- 🧪 **Full testing suite** with Molecule framework integration
- 📝 **Professional documentation** with examples and troubleshooting guides

### Core Features 🎯
- **Authentication**: Automated network joining with auth keys and custom arguments
- **Repository Management**: Dynamic URL generation based on distribution and track
- **Service Control**: Configurable startup behavior with auto-detection capabilities  
- **Validation**: Multi-layered system checks based on official Tailscale installer
- **Error Handling**: Graceful error collection with comprehensive diagnostic reports
- **Cross-Platform**: Support for multiple architectures (x86_64, aarch64, armv7l)

### Technical Specifications 🛠️
- **Minimum Ansible Version**: 2.15+
- **Python Version**: 3.9+
- **License**: Apache-2.0
- **Company**: EWARE
- **Author**: Grzegorz Franus
- **Quality**: All yamllint and ansible-lint checks passing

### Configuration Variables 📝
- Authentication: `tailscale_auth_key`, `tailscale_extra_args`
- Installation: `tailscale_track`, `tailscale_apt_key_type`, `tailscale_install_method`
- Service: `tailscale_service_enabled`, `tailscale_systemctl_start`, `tailscale_service_manager`
- Validation: `tailscale_validate_system`, `tailscale_validate_connectivity`
- Diagnostics: `tailscale_enhanced_error_reporting`, `tailscale_collect_system_info`

### File Structure 📁
```
ansible-role-tailscale/
├── defaults/main.yml           # Default configuration variables
├── handlers/main.yml           # Service restart handlers
├── meta/main.yml              # Role metadata
├── tasks/
│   ├── main.yml               # Main orchestration
│   ├── assert.yml             # Variable validation
│   ├── prerequisites.yml      # System preparation
│   ├── repository.yml         # Repository configuration
│   ├── install.yml            # Package installation
│   ├── service.yml            # Service management
│   ├── validate.yml           # System validation
│   └── error_reporting.yml    # Error diagnostics
├── vars/
│   ├── main.yml               # Internal variables
│   ├── debian_12.yml          # Debian 12 specific
│   └── ubuntu_24.04.yml       # Ubuntu 24.04 specific
└── molecule/default/          # Testing framework
```

### Supported Platforms 🌍
| OS Family | Version | GPG Method | Status |
|-----------|---------|------------|---------|
| Ubuntu | 24.04 (Noble) | Keyring | ✅ Full Support |
| Ubuntu | 22.04 (Jammy) | Keyring | ✅ Full Support |
| Debian | 12 (Bookworm) | Keyring | ✅ Full Support |
| Debian | 11 (Bullseye) | Legacy | ✅ Full Support |
