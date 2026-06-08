````markdown
# FINAL HIERARCHY - [GLOBAL]

ansible/
├── ansible.cfg
├── Makefile
├── requirements.yml
├── .gitignore
├── README.md
│
├── inventories/
│   ├── production/
│   │   ├── hosts
│   │   ├── group_vars/
│   │   │   ├── all.yml
│   │   │   ├── docker.yml
│   │   │   └── opencode.yml
│   │   ├── host_vars/
│   │   └── inventory_plugins/
│   │
│   └── staging/
│       ├── hosts
│       ├── group_vars/
│       │   ├── all.yml
│       │   ├── docker.yml
│       │   └── opencode.yml
│       ├── host_vars/
│       └── inventory_plugins/
│
├── playbooks/
│   ├── docker/
│   │   ├── install_docker.yml
│   │   └── uninstall_docker.yml
│   │
│   └── opencode/
│       ├── install_opencode.yml
│       └── uninstall_opencode.yml
│
├── roles/
│   │
│   ├── docker_ce/
│   │   ├── defaults/
│   │   │   └── main.yml
│   │   │
│   │   ├── tasks/
│   │   │   ├── main.yml
│   │   │   ├── install.yml
│   │   │   ├── install_debian.yml
│   │   │   ├── install_redhat.yml
│   │   │   ├── uninstall.yml
│   │   │   ├── verify.yml
│   │   │   └── verify_uninstall.yml
│   │   │
│   │   ├── handlers/
│   │   │   └── main.yml
│   │   │
│   │   ├── templates/
│   │   │   └── daemon.json.j2
│   │   │
│   │   ├── files/
│   │   │
│   │   ├── vars/
│   │   │   └── main.yml
│   │   │
│   │   ├── meta/
│   │   │   └── main.yml
│   │   │
│   │   ├── molecule/
│   │   │   └── default/
│   │   │       ├── converge.yml
│   │   │       ├── verify.yml
│   │   │       └── molecule.yml
│   │   │
│   │   └── README.md
│   │
│   └── opencode/
│       ├── defaults/
│       │   └── main.yml
│       │
│       ├── tasks/
│       │   ├── main.yml
│       │   ├── install.yml
│       │   ├── uninstall.yml
│       │   ├── verify.yml
│       │   └── verify_uninstall.yml
│       │
│       ├── handlers/
│       │   └── main.yml
│       │
│       ├── templates/
│       │
│       ├── files/
│       │
│       ├── vars/
│       │   └── main.yml
│       │
│       ├── meta/
│       │   └── main.yml
│       │
│       ├── molecule/
│       │   └── default/
│       │       ├── converge.yml
│       │       ├── verify.yml
│       │       └── molecule.yml
│       │
│       └── README.md
│
├── collections/ 
│   ├── ansible
│   └── ansible_collections/
│       └──GALAXY.yml 
│
├── logs/
│   └── ansible.log
│
├── scripts/
│
└── .ansible/




# Ansible Infrastructure

Production-grade Ansible infrastructure for automated software provisioning, installation, verification, and lifecycle management.

This repository follows modern Ansible best practices and is designed for:

- Production environments
- Staging environments
- CI/CD pipelines
- GitOps workflows
- Infrastructure-as-Code (IaC)
- Reusable role-based automation
- Multi-platform Linux support

Supported platforms:

- Debian
- Ubuntu
- CentOS
- RedHat Enterprise Linux (RHEL)

---

# Architecture Goals

This infrastructure was designed with the following principles:

- Modular role-based architecture
- Safe-by-default automation
- Idempotent operations
- Rollback-aware uninstall flows
- Production-grade package management
- GPG repository verification
- Version pinning support
- Separation of environments
- Extensible inventory structure
- Multi-role scalability
- Enterprise-ready automation patterns

---

# Repository Structure

```text
ansible/
├── ansible.cfg
├── Makefile
├── requirements.yml
├── .gitignore
├── README.md
│
├── inventories/
│   ├── production/
│   └── staging/
│
├── playbooks/
│   ├── docker/
│   └── opencode/
│
├── roles/
│   ├── docker_ce/
│   └── opencode/
│
├── collections/
├── logs/
├── scripts/
└── .ansible/
````

---

# Core Components

## inventories/

Environment-specific inventory configuration.

Contains:

* hosts
* group variables
* host variables
* inventory plugins

Example:

```text
inventories/
├── production/
└── staging/
```

This allows:

* environment isolation
* different variables per environment
* future cloud inventory plugins
* scalable infrastructure targeting

---

## playbooks/

High-level orchestration entrypoints.

Each software/service has dedicated install and uninstall playbooks.

Example:

```text
playbooks/docker/install_docker.yml
playbooks/docker/uninstall_docker.yml
```

Playbooks remain intentionally minimal.

Business logic belongs inside roles.

---

## roles/

Main automation logic.

Each role is fully isolated and reusable.

Example:

```text
roles/docker_ce/
roles/opencode/
```

Every role follows modern Ansible structure:

```text
tasks/
handlers/
defaults/
templates/
vars/
meta/
molecule/
```

---

# Docker Role

## Features

The Docker role provides production-grade Docker CE installation.

### Supported Features

* Official Docker CE repositories
* Modern GPG verification
* Signed repository configuration
* Docker Compose plugin
* Docker Buildx plugin
* containerd.io installation
* Version pinning support
* Runtime verification
* Safe uninstall
* Optional data cleanup
* Daemon configuration management

---

## Docker Security Practices

This infrastructure uses modern Docker repository security practices.

### Correct Modern Approach

```text
/etc/apt/keyrings/docker.asc
signed-by=
```

### Deprecated Approach NOT Used

```text
apt-key add
```

The deprecated method should never be used in modern production systems.

---

## Docker Version Pinning

Supported through:

```yaml
docker_version: ""
```

Example:

```yaml
docker_version: "5:28.0.1-1~ubuntu.24.04~noble"
```

Why version pinning matters:

* Prevent unexpected upgrades
* Prevent Compose incompatibilities
* Prevent Kubernetes runtime issues
* Prevent daemon/runtime mismatches
* Improve reproducibility
* Improve rollback stability

---

## Docker Uninstall Safety

The uninstall flow is intentionally safe-by-default.

By default:

```yaml
docker_remove_data: false
```

This prevents accidental deletion of:

```text
/var/lib/docker
/var/lib/containerd
```

This is critical for production systems.

Data removal only occurs when explicitly enabled.

---

# OpenCode Role

## Features

The OpenCode role provides:

* User-scoped installation
* Local binary management
* Installation verification
* Safe uninstall
* Cache cleanup
* Config cleanup
* Legacy cleanup handling

Supports:

* Local developer environments
* Staging environments
* CI runners
* Multi-user systems

---

# Handlers

Handlers are used for reactive operations.

Example:

* Restart Docker daemon
* Update APT cache

This improves:

* idempotency
* performance
* task orchestration

---

# Templates

Templates are used for dynamic configuration generation.

Example:

```text
roles/docker_ce/templates/daemon.json.j2
```

Used for:

* Docker daemon configuration
* Future runtime customization
* Environment-specific configs

---

# Verification Strategy

Verification tasks validate actual runtime behavior instead of only package presence.

Examples:

* docker version
* docker info
* docker compose version
* docker buildx version
* systemctl status docker

This prevents false-positive installations.

---

# Makefile Automation

The repository uses a centralized Makefile for simplified operations.

Examples:
(sudo)
```bash
make install-docker-prod
make uninstall-docker-prod

make install-opencode-prod
make uninstall-opencode-prod
```

This provides:

* repeatable execution
* simplified operator workflows
* CI/CD integration consistency

---

# Ansible Collections

Collections extend Ansible functionality.

Installed using:

```bash
ansible-galaxy collection install -r requirements.yml -p ./collections
```

Examples:

* ansible.posix
* community.general

Collections provide:

* additional modules
* plugins
* filters
* inventory integrations

---

# Supported Installation Methods

## Docker

Current:

```yaml
docker_install_method: official
```

Future extensibility:

```yaml
docker_install_method:
  - official
  - distro
```

Allows:

* enterprise repo control
* air-gapped environments
* mirrored repositories
* custom package sources

---

# Molecule Testing

Roles include Molecule structure for future automated testing.

Example:

```text
roles/docker_ce/molecule/
```

This enables:

* CI validation
* idempotency testing
* multi-platform testing
* regression prevention

---

# Logging

Ansible logs are stored in:

```text
logs/ansible.log
```

Configured through:

```ini
log_path = logs/ansible.log
```

Useful for:

* auditing
* debugging
* CI troubleshooting
* deployment tracing

---

# Recommended Usage

## Install Docker 
(sudo)
```bash
make install-docker-prod
or
make install-docker-staging
```

## Uninstall Docker
(sudo)
```bash
make uninstall-docker-prod
or
make uninstall-docker-staging
```

## Install OpenCode
(sudo)
```bash
make install-opencode-prod
or
make install-opencode-staging
```

## Uninstall OpenCode
(sudo)
```bash
make uninstall-opencode-prod
or
make uninstall-opencode-staging
```

---

# Recommended Future Improvements

Future production improvements may include:

* Rootless Docker support
* SELinux automation
* AppArmor profiles
* CIS hardening
* Air-gapped repository support
* Private registry authentication
* Docker daemon TLS
* Kubernetes runtime integration
* Dynamic inventories
* Vault integration
* GitHub Actions integration
* GitLab CI integration
* Automated Molecule testing
* Multi-node orchestration
* Secret management
* Observability integration

---

# Design Philosophy

This infrastructure follows several core engineering principles:

## Safe-by-default

Dangerous operations require explicit enablement.

## Idempotent

Repeated runs should produce predictable results.

## Production-first

Designed for real infrastructure, not only local labs.

## Extensible

New services and roles can be added without restructuring the repository.

## Environment-isolated

Production and staging remain logically separated.

## Verification-driven

Installations are validated through actual runtime checks.

---

# Requirements

## Ansible

Recommended:

```text
ansible-core >= 2.15
```

## Python

Recommended:

```text
Python >= 3.10
```

## Linux

Supported:

* Debian
* Ubuntu
* CentOS
* RedHat Enterprise Linux

---

# Installation

Install collections:
(sudo)
```bash
ansible-galaxy collection install -r requirements.yml -p ./collections
```

Run playbooks:

```bash
(sudo) make install-docker-prod
(sudo) make install-opencode-prod
```

---

# License

Internal infrastructure automation repository.

```
```


