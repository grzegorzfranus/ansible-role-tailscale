# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.8.3](https://github.com/grzegorzfranus/ansible-role-tailscale/compare/v1.8.2...v1.8.3) (2026-06-29)


### Miscellaneous

* migrate workflows to github-workflows and align configuration ([#20](https://github.com/grzegorzfranus/ansible-role-tailscale/issues/20)) ([6866e5e](https://github.com/grzegorzfranus/ansible-role-tailscale/commit/6866e5e2d83ce1347e8fc40dd782d47287c0ca3d))

## [1.8.2](https://github.com/grzegorzfranus/ansible-role-tailscale/compare/v1.8.1...v1.8.2) (2026-06-01)


### Code Refactoring

* standardize comments ([#17](https://github.com/grzegorzfranus/ansible-role-tailscale/issues/17)) ([948de25](https://github.com/grzegorzfranus/ansible-role-tailscale/commit/948de2597d4192971f051518496d9b37b3775aa2))

## [1.8.1](https://github.com/grzegorzfranus/ansible-role-tailscale/compare/v1.8.0...v1.8.1) (2026-06-01)


### Documentation

* unify README structure and improve variable assertions ([#15](https://github.com/grzegorzfranus/ansible-role-tailscale/issues/15)) ([82ae2b3](https://github.com/grzegorzfranus/ansible-role-tailscale/commit/82ae2b36e6245362205a361999e6e3bcda4c6b5a))

## [1.8.0](https://github.com/grzegorzfranus/ansible-role-tailscale/compare/v1.7.0...v1.8.0) (2026-05-22)


### Features

* **ci:** add PR title validation to CI pipeline ([#13](https://github.com/grzegorzfranus/ansible-role-tailscale/issues/13)) ([ccad4fc](https://github.com/grzegorzfranus/ansible-role-tailscale/commit/ccad4fc22c6240353307f217d10fb6cd8ca527ca))

## [1.7.0](https://github.com/grzegorzfranus/ansible-role-tailscale/compare/v1.6.1...v1.7.0) (2026-05-22)


### Features

* **ci:** enforce conventional commits rule ([8545460](https://github.com/grzegorzfranus/ansible-role-tailscale/commit/854546011ccc4d21cfc794acbe0cb6a8938adf47))

## [1.6.1](https://github.com/grzegorzfranus/ansible-role-tailscale/compare/v1.6.0...v1.6.1) (2026-05-21)


### Bug Fixes

* remove duplicate CI runs, fix Galaxy publish, update docs ([#9](https://github.com/grzegorzfranus/ansible-role-tailscale/issues/9)) ([a4d80dc](https://github.com/grzegorzfranus/ansible-role-tailscale/commit/a4d80dc4bd1cd7c4b792f228ea2199c0122f1a7a))

## [1.6.0](https://github.com/grzegorzfranus/ansible-role-tailscale/compare/v1.5.0...v1.6.0) (2026-05-21)


### Features

* add automated versioning and Galaxy publish pipeline ([#7](https://github.com/grzegorzfranus/ansible-role-tailscale/issues/7)) ([91f460e](https://github.com/grzegorzfranus/ansible-role-tailscale/commit/91f460eb65bc39fadb6a8947eea8903272d233b7))
* migrate CI to centralized reusable workflow ([#6](https://github.com/grzegorzfranus/ansible-role-tailscale/issues/6)) ([4c0fd79](https://github.com/grzegorzfranus/ansible-role-tailscale/commit/4c0fd795664e7bc6f61185529cb632c2a78d6a5e))

## [1.5.0] - 2026-05-18

### Added
- **Enterprise Linux 9 support**: Rocky Linux 9, RHEL 9, AlmaLinux 9, CentOS Stream 9
- New `vars/redhat_9.yml` with EL9-specific repository URLs, prerequisites, and logging defaults
- New `tasks/repository_redhat.yml` with DNF repository and RPM GPG key management
- New `tasks/repository_debian.yml` extracted from original `repository.yml`
- RedHat-family diagnostics in error reporting (checks `/etc/yum.repos.d` and `/etc/pki/rpm-gpg`)
- Rocky Linux 9 added to CI/CD molecule test matrix
- RPM-based package and repository verification in all molecule scenarios

### Changed
- `tasks/repository.yml` refactored to OS-family dispatcher pattern (includes `repository_debian.yml` or `repository_redhat.yml`)
- `tasks/validate.yml` extended for RedHat OS family, EL9 distribution checks, and OS-aware connectivity tests
- `tasks/assert.yml` relaxed repo key URL regex (supports both `.gpg` and RedHat patterns), `apt_key_type` validation skipped on RedHat
- `tasks/remove.yml` OS-family conditions added to Debian tasks, new RedHat removal tasks (DNF repo, RPM key)
- `tasks/logging.yml` uses OS-aware log user/group (`root:root` on EL9 via `__tailscale_os_log_user/group`)
- `tasks/error_reporting.yml` OS-family conditions for repository and key diagnostics, OS-aware syslog path
- `vars/main.yml` added `dnf` to supported package managers, RedHat repo URL fallback, OS-specific logging defaults
- `meta/main.yml` added EL platform with version 9
- `meta/argument_specs.yml` updated description to include EL9
- All molecule scenarios updated with RedHat pre_tasks and preparation steps
- Service file verification in molecule checks both Debian (`/lib/systemd/system/`) and RedHat (`/usr/lib/systemd/system/`) paths

## [1.4.1] - 2026-05-18

### Fixed
- Upgraded `actions/checkout` from v4 to v6 (Node.js 24 compatible)
- Upgraded `actions/setup-python` from v5 to v6 (Node.js 24 compatible)

### Changed
- Standardized workflow and job naming to enterprise convention (Numbered Title Case)
- Updated `README.md` file structure documentation to remove emojis and include the publish workflow

## [1.4.0] - 2026-04-24

### ⚠️ Breaking Changes
- **Variable naming convention**: All internal variables renamed from `_tailscale_*` (single underscore) to `__tailscale_*` (double underscore) per Red Hat CoP §3.1.4
- **OS-specific variables renamed** to internal namespace:
  - `tailscale_repo_key_url` → `__tailscale_os_repo_key_url`
  - `tailscale_repo_key_path` → `__tailscale_os_repo_key_path`
  - `tailscale_repo_list_url` → `__tailscale_os_repo_list_url`
  - `tailscale_prerequisites` (in OS-specific files) → `__tailscale_os_prerequisites`
  - `tailscale_service_name` → `__tailscale_service_name`
- **Output facts renamed** to internal namespace:
  - `tailscale_system_info` → `__tailscale_system_info`
  - `tailscale_installation_info` → `__tailscale_installation_info`
  - `tailscale_error_report` → `__tailscale_error_report_full`
  - `tailscale_up_result` → `__tailscale_up_result`
- User-overridable `tailscale_prerequisites` in `defaults/main.yml` is now merged with OS-specific packages at runtime

### Added ✅
- **`meta/argument_specs.yml`**: Ansible-native argument validation (CoP §3.1.20) covering all 25+ public variables with types, choices, defaults, and descriptions
- **Prerequisites merge pattern**: `tailscale_prerequisites` (user-defined) + `__tailscale_os_prerequisites` (OS-specific) merged at runtime via `__tailscale_merged_prerequisites`
- **Dynamic fallback URLs** in `vars/main.yml` for unsupported OS variants — OS-specific files override these with static values
- **`molecule/logging/prepare.yml`**: New prepare playbook for logging scenario (previously missing)
- **`molecule/uninstall/prepare.yml`**: New prepare playbook for uninstall scenario (previously missing)

### Changed 🔄
- **ansible-lint profile**: Upgraded from `min` to `shared` (now passes `production` profile)
- **Removed `meta-no-info` skip** from `.ansible-lint` (no longer needed with argument_specs)
- **Debug tasks**: All `ansible.builtin.debug` tasks now include `verbosity: 1` to avoid cluttering production output (CoP §5.4)
- **`when:` conditions**: All multi-condition `when:` blocks converted from `>` folding to list format (CoP §8.2)
- **`failed_when:` on auth task**: Converted to list format for readability
- **Rsyslog template backup**: Changed from `backup: false` to `backup: true` (CoP §3.1.14)
- **Comments reduced**: Removed verbose banner comments (`=====`, `-----`) from all task and molecule files; each file now has a single-line header (CoP §8.3)
- **Emoji removed** from all `fail_msg`, `success_msg`, and `debug` messages in role tasks for enterprise-grade log output

### Molecule Tests 🧪
- **Deprecated facts replaced**: All `ansible_os_family`, `ansible_service_mgr`, `ansible_pkg_mgr` → `ansible_facts['key']` format across all scenarios
- **Emoji removed** from all Molecule task names for cleaner log parsing and CI output
- **`shell` → `file` module**: `default/prepare.yml` shadow permissions fix now uses `ansible.builtin.file` instead of `ansible.builtin.shell`
- **Dot notation → bracket notation**: All `.stat.exists` and `.rc` references converted to bracket `['stat']['exists']` format
- **`vars_files` removed** from `default/verify.yml` — tests now verify effects directly, not role internals
- **Old variable references fixed**: `tailscale_service_name` → hardcoded `tailscaled` in `logging/verify.yml`
- **Deprecated `provisioner.lint`** removed from all `molecule.yml` (deprecated in Molecule 6+)

### Code Quality 🎯
- All files pass `yamllint` with zero errors
- All files pass `ansible-lint` with zero failures, zero warnings
- Passes `production` ansible-lint profile (higher than required `shared`)
- Full compliance with [Red Hat CoP Automation Good Practices](https://redhat-cop.github.io/automation-good-practices/)

## [1.3.0] - 2026-03-15

### Fixed 🔧
- Fixed duplicate section "3." numbering in `defaults/main.yml`
- Fixed section numbering sequence in `tasks/repository.yml`
- Removed dead OpenRC code and undefined variable references in `tasks/service.yml`
- Removed unsupported package managers from `_tailscale_supported_package_managers` list in `vars/main.yml`
- Removed redundant cache update task in `tasks/prerequisites.yml`
- Fixed incomplete file structure diagram in `README.md`
- Updated default testing image to `ubuntu2404` in `molecule/default/molecule.yml` to prevent failing on unsupported OS checks

### Changed 🔄
- Added support for Debian 13 (Trixie)
- Bumped `min_ansible_version` to "2.16" in `meta/main.yml`

## [1.2.3] - 2025-11-30

### Fixed 🔧
- Removed emojis from handler names for cleaner output and better compatibility
- Fixed deprecated `ansible_service_mgr` in handler condition, replaced with `ansible_facts['service_mgr']`
- Updated all `notify` references in `tasks/logging.yml` and `tasks/service.yml` to match handler names without emojis

### Changed 🔄
- Handler names now follow consistent format without emoji prefixes: `Tailscale | handlers | <action>`

## [1.2.2] - 2025-11-23

### Fixed 🔧
- Fixed compatibility issue with Ansible 2.20+ that caused "Recursive loop detected in template" error in variable validation
- Removed redundant `tailscale_auth_key` variable assignment in example playbook that created circular reference
- Enhanced variable handling to prevent template recursion errors in assertion tasks
- Fixed `tailscale_extra_args` validation regex to allow spaces and additional command-line characters (spaces, forward slash, colon, brackets)
- Updated regex pattern from `^[a-zA-Z0-9=._\\-]*$` to `^[a-zA-Z0-9=._\\-\\s/:\\[\\]]*$` for proper multi-flag argument validation
- **Replaced all deprecated `ansible_*` facts with `ansible_facts['fact_name']` format** to eliminate deprecation warnings and prepare for Ansible 2.24+
- Removed invalid `systemd-analyze verify` validation from systemd drop-in configuration file deployment (systemd-analyze only works with full .service files, not drop-in overrides)
- Updated systemd service override template to use `StandardOutput=journal` instead of obsolete `StandardOutput=syslog` to eliminate systemd deprecation warnings

### Compatibility 🔄
- Verified full compatibility with Ansible 2.20 and newer versions
- Maintained backward compatibility with Ansible 2.15+
- Prepared for Ansible 2.24 by migrating away from deprecated `INJECT_FACTS_AS_VARS` behavior
- All role tasks now use modern `ansible_facts` dictionary format instead of top-level injected variables

### Validation Improvements 📈
- Enhanced `tailscale_extra_args` validation to support complex command-line arguments with multiple flags separated by spaces
- Examples now supported: `--hostname=server.example.com --accept-routes --accept-dns=false`

### Code Quality 🎯
- Systematic migration of 60+ instances of deprecated fact variables across all task files
- Updated variable references in: `main.yml`, `validate.yml`, `error_reporting.yml`, `service.yml`, `repository.yml`, `remove.yml`, `logging.yml`, `prerequisites.yml`, `install.yml`, and `vars/main.yml`
- No deprecation warnings when running with Ansible 2.20+
- **Important**: Only setup module facts migrated to `ansible_facts` dictionary; special Ansible variables (`ansible_version`, `ansible_connection`, `ansible_check_mode`, `inventory_hostname`, `playbook_dir`) remain as top-level variables per Ansible design

## [1.2.1] - 2025-09-04

### Changed 🔄
- Standardized all task names to convention: `Tailscale | section | description` (removed emojis from task names)
- Converted all `when:` clauses to folded style for consistency
- Corrected tags in `tasks/main.yml` to allowed set (`requirements`, `configure`)
- Added template validation for rsyslog, systemd override, and logrotate

### Documentation 📝
- Updated README tags list to allowed tags and usage
- Added `tailscale_state` to variables table and clarified “Service State and Configuration”

### Quality Improvements 📈
- Expanded variable assertions in `tasks/assert.yml` (service state, install method, apt key type, validation and logging flags, logrotate structure)

## [1.2.0] - 2025-08-11

### Changed 🔄
- 🔐 Secure authentication task: added `no_log: true` and safe quoting for `--authkey` to prevent secrets exposure in logs
- 📦 Optimized prerequisites installation by installing the whole list in a single package task
 - 🧹 Pruned legacy `apt-key` and OpenRC paths; enforced keyring + systemd only
 - 📦 Switched repository management to `apt_repository` with `signed-by`
 - ✅ Strengthened validation: choices for track/service manager, numeric ranges
 - 🧪 Improved check-mode behavior (skip mutations, keep info)
 - 🧩 Converted systemd override to managed template `templates/systemd/logging.conf.j2`
 - 🧼 Added uninstall support via `tailscale_state: absent`
 - 🌐 Added timeouts/retries for network operations

### Documentation 📝
- Updated supported OS matrix in `README.md` to reflect current support (Ubuntu 24.04, Debian 12)
- Added sections: Security considerations, Check mode behavior, Tags usage, Network resilience, Uninstall and Repository management

## [1.1.0] - 2025-06-29

### Added ✅
- 📁 **Optional Dedicated Logging Configuration** - New comprehensive logging system for Tailscale
  - `tailscale_enable_logging` flag to control separate log files (disabled by default)
  - Creates `/var/log/tailscale/tailscale.log` for Tailscale-specific logs separated from system syslog
  - RSyslog integration with automatic service configuration and log filtering
  - Systemd service override for proper syslog identifier configuration
  - Advanced logrotate configuration with dictionary structure for easy management
  - Support for copytruncate, dateext, custom date formats, and archive directories
  - Automatic creation of logrotate archive directories when olddir is specified
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

### Technical Improvements 🛠️
- **RSyslog Integration**: Automatic service configuration and log filtering
- **Systemd Override**: Proper syslog identifier for clean log separation  
- **Dictionary Structure**: Organized `tailscale_logrotate_options` replacing 9+ individual variables
- **Directory Management**: Automatic creation of logrotate olddir with proper permissions
- **Professional Templates**: Production-ready rsyslog and logrotate configurations

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
