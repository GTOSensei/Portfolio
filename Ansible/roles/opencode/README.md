````markdown id="y0d4pz"
# opencode Ansible Role

Production-ready Ansible role for installing, verifying, managing, and safely uninstalling OpenCode AI in local and multi-user Linux environments.

This role automates the complete lifecycle of OpenCode installation while following modern Ansible best practices, safe-by-default behavior, and idempotent infrastructure automation principles.

Designed for:

- Developer workstations
- CI/CD runners
- Local AI environments
- Shared Linux systems
- DevSecOps labs
- Staging environments
- Production automation workflows

Supported platforms:

- Debian
- Ubuntu
- CentOS
- RedHat Enterprise Linux (RHEL)

---

# Features

## Installation

- User-scoped installation
- Automatic target user detection
- Installer download automation
- Local binary installation
- Legacy installation cleanup
- Idempotent installation flow
- Runtime verification

---

## Uninstall

- Safe removal process
- User installation cleanup
- Cache cleanup
- Config cleanup
- Temporary installer cleanup
- Legacy symlink cleanup
- Legacy root installation cleanup
- Runtime verification after uninstall

---

## Verification

The role validates actual runtime functionality instead of only checking file existence.

Verification includes:

- binary availability
- executable resolution
- version validation
- uninstall verification

---

# Repository Structure

```text
roles/opencode/
├── defaults/
│   └── main.yml
│
├── tasks/
│   ├── main.yml
│   ├── install.yml
│   ├── uninstall.yml
│   ├── verify.yml
│   └── verify_uninstall.yml
│
├── handlers/
│   └── main.yml
│
├── templates/
│
├── files/
│
├── vars/
│   └── main.yml
│
├── meta/
│   └── main.yml
│
├── molecule/
│   └── default/
│
└── README.md
````

---

# Installation Flow

The installation process performs:

1. Determine target installation user
2. Resolve target user home directory
3. Install required dependencies
4. Verify existing installation state
5. Download OpenCode installer
6. Remove legacy installations
7. Execute installer as target user
8. Verify runtime functionality
9. Display installed version information

---

# User-Aware Installation

The role automatically detects the correct target user.

Example logic:

```text id="6knml2"
SUDO_USER
```

Fallback behavior:

```text id="u2dcbm"
ansible_facts['user_id']
```

This ensures:

* correct HOME resolution
* correct user permissions
* proper local installation
* safe multi-user execution

---

# Installation Location

OpenCode is installed inside the target user's home directory.

Default installation path:

```text id="wbvgpb"
~/.opencode
```

Binary location:

```text id="4v1k07"
~/.opencode/bin/opencode
```

This avoids:

* global package pollution
* system-wide conflicts
* unsafe root-only execution

---

# Local Binary Management

The role manages:

```text id="kz6k2v"
~/.local/bin/opencode
```

Supports:

* local shell usage
* user-scoped environments
* isolated installations

---

# Legacy Cleanup Support

The role automatically removes legacy installation artifacts.

Examples:

```text id="xpcg4q"
/usr/local/bin/opencode
/root/.opencode
```

This prevents:

* broken symlinks
* outdated installations
* conflicting runtime behavior

---

# Temporary Installer Management

Installer downloads are managed safely.

Example temporary path:

```text id="9mjlwm"
/tmp/opencode-install.sh
```

Temporary installation artifacts are removed during cleanup/uninstall flows.

---

# Cache and Config Cleanup

The uninstall flow removes:

```text id="7u8prj"
~/.cache/opencode
~/.config/opencode
```

This provides clean uninstall behavior while remaining safe and predictable.

---

# Runtime Verification

The role verifies actual runtime functionality.

Verification includes:

```bash id="6v4k7t"
command -v opencode
opencode --version
```

This prevents false-positive installation states.

---

# Safe Uninstall Philosophy

The uninstall process is intentionally conservative.

The role removes:

* binaries
* symlinks
* configuration
* cache
* temporary installers

But avoids:

* unrelated user files
* arbitrary HOME cleanup
* destructive system modifications

---

# Role Variables

## Main Variables

Defined in:

```text id="p6z8af"
defaults/main.yml
```

---

## Installation State

```yaml id="72y2lx"
opencode_state: present
```

Controls:

* installation
* uninstall flow

Possible values:

```yaml id="mw6vlq"
present
absent
```

---

## Installer URL

```yaml id="5jczdq"
opencode_install_url: "https://opencode.ai/install"
```

Controls installer source location.

Can be overridden for:

* mirrored repositories
* internal artifact systems
* air-gapped environments

---

## Installer Temporary Path

```yaml id="l0bdv3"
opencode_installer_path: "/tmp/opencode-install.sh"
```

---

## Temporary Working Directory

```yaml id="r4dbu5"
opencode_temp_dir: "/tmp/opencode"
```

---

# Example Playbook

## Install OpenCode

```yaml id="0bavdk"
---
- name: Install OpenCode
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    opencode_state: present

  roles:
    - role: opencode
```

---

## Uninstall OpenCode

```yaml id="mk71gt"
---
- name: Uninstall OpenCode
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    opencode_state: absent

  roles:
    - role: opencode
```

---

# Verification Tasks

## Install Verification

Verification tasks include:

* executable path validation
* version validation
* runtime command execution

---

## Uninstall Verification

Verification tasks ensure:

* binary removal
* command absence
* successful cleanup state

---

# Idempotency

This role is fully idempotent.

Repeated executions:

* do not reinstall unnecessarily
* do not duplicate configuration
* safely converge to desired state

---

# Multi-User Safety

The role is designed for safe execution on systems with multiple users.

Installation is scoped to the detected target user.

This prevents:

* incorrect HOME usage
* permission mismatches
* root-owned user files

---

# CI/CD Compatibility

The role supports:

* GitHub Actions
* GitLab CI
* Jenkins
* local runners
* ephemeral environments

Supports:

```yaml id="z4hmvn"
CI: "true"
```

during installation execution.

---

# Recommended Future Improvements

Possible future enhancements:

* checksum validation
* GPG signature verification
* offline installation support
* release pinning
* automatic update management
* shell profile integration
* rootless isolated environments
* system-wide installation mode
* air-gapped deployment support
* artifact caching
* Molecule testing scenarios

---

# Molecule Testing

The repository includes Molecule structure for future automated testing.

Location:

```text id="wyj2gm"
molecule/default/
```

Supports future:

* CI validation
* idempotency testing
* regression prevention
* multi-platform testing

---

# Requirements

## Ansible

Recommended:

```text id="gr7f2v"
ansible-core >= 2.15
```

## Python

Recommended:

```text id="ok6x9f"
Python >= 3.10
```

---

# Design Principles

This role follows several engineering principles.

## User-scoped automation

Installations should remain isolated to the target user.

## Safe-by-default

Avoid destructive system-wide operations.

## Verification-driven

Validate actual runtime behavior.

## Idempotent

Repeated runs should remain predictable.

## Extensible

Future installation methods and runtime behaviors can be added cleanly.

## Production-aware

Designed for real automation environments, not only local testing.

---

# Recommended Usage

## Install OpenCode

```bash id="x5jq0t"
make install-opencode-prod
```

## Uninstall OpenCode

```bash id="40g9ut"
make uninstall-opencode-prod
```

---

# License

Internal infrastructure automation role.

```
```
