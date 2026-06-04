````markdown id="e9rb1v"
# docker_ce Ansible Role

Production-grade Docker CE installation and lifecycle management role for Ansible.

This role installs, configures, verifies, and safely uninstalls Docker CE using official Docker repositories and modern Linux packaging practices.

Designed for:

- Production environments
- Staging environments
- CI/CD pipelines
- GitOps workflows
- Kubernetes worker nodes
- Developer workstations
- Automated infrastructure provisioning

Supported operating systems:

- Debian
- Ubuntu
- CentOS
- RedHat Enterprise Linux (RHEL)

---

# Features

## Installation

- Official Docker CE repository installation
- GPG repository verification
- Modern `signed-by` repository configuration
- Docker Compose plugin installation
- Docker Buildx plugin installation
- containerd.io installation
- Multi-platform architecture support
- Package version pinning
- Runtime verification
- Idempotent execution

---

## Security

- No deprecated `apt-key add`
- Dedicated GPG keyrings
- Signed repository validation
- Explicit repository trust configuration
- Optional certificate validation controls
- Safe uninstall behavior
- No automatic runtime data deletion

---

## Uninstall

- Safe package removal
- Repository cleanup
- GPG key cleanup
- Optional runtime data deletion
- Systemd cleanup
- Runtime verification after uninstall

---

## Runtime Configuration

- Docker daemon configuration templating
- Logging configuration
- OverlayFS configuration
- Live restore support
- Service enablement management
- Service startup management

---

# Repository Structure

```text
roles/docker_ce/
├── defaults/
│   └── main.yml
│
├── tasks/
│   ├── main.yml
│   ├── install.yml
│   ├── install_debian.yml
│   ├── install_redhat.yml
│   ├── uninstall.yml
│   ├── verify.yml
│   └── verify_uninstall.yml
│
├── handlers/
│   └── main.yml
│
├── templates/
│   └── daemon.json.j2
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

## Debian / Ubuntu

The role performs the following operations:

1. Remove conflicting legacy Docker packages
2. Install required prerequisites
3. Create secure APT keyrings directory
4. Download official Docker GPG key
5. Configure signed Docker repository
6. Update package cache
7. Install Docker CE packages
8. Configure Docker daemon
9. Enable Docker service
10. Start Docker service
11. Verify runtime functionality

---

## RedHat / CentOS

The role performs:

1. Remove legacy Docker packages
2. Install required prerequisites
3. Add official Docker repository
4. Install Docker CE packages
5. Configure Docker daemon
6. Enable Docker service
7. Start Docker service
8. Verify runtime functionality

---

# Modern Repository Security

This role uses modern repository signing practices.

## Correct Modern Debian Approach

```text id="bt9f4n"
/etc/apt/keyrings/docker.asc
```

Repository example:

```text id="1r7f1l"
signed-by=/etc/apt/keyrings/docker.asc
```

---

## Deprecated Method NOT Used

```text id="gmlj8r"
apt-key add
```

This deprecated approach should never be used in modern production systems.

---

# Role Variables

## Main Variables

Defined in:

```text id="bfevnm"
defaults/main.yml
```

---

## Installation State

```yaml id="rrs9ga"
docker_state: present
```

Controls:

* installation
* uninstall flow

Possible values:

```yaml id="3mfgdu"
present
absent
```

---

## Installation Method

```yaml id="exr7yk"
docker_install_method: official
```

Future extensibility:

```yaml id="u7pw9h"
official
distro
```

---

## Docker Edition

```yaml id="ijqizd"
docker_edition: ce
```

---

## Package Version Pinning

```yaml id="1rw9j0"
docker_version: ""
```

Example:

```yaml id="xkzzpb"
docker_version: "5:28.0.1-1~ubuntu.24.04~noble"
```

Production systems should pin versions to prevent:

* unexpected upgrades
* Compose incompatibilities
* Kubernetes runtime mismatches
* Swarm instability
* daemon behavior drift

---

## Docker Packages

```yaml id="fgrwma"
docker_packages:
  - docker-ce
  - docker-ce-cli
  - containerd.io
  - docker-buildx-plugin
  - docker-compose-plugin
```

---

## Runtime Data Removal Safety

```yaml id="0xy6jg"
docker_remove_data: false
```

This is intentionally disabled by default.

Protects:

```text id="eu4v3i"
/var/lib/docker
/var/lib/containerd
```

This prevents catastrophic data loss during uninstall operations.

Runtime data is only removed when explicitly enabled.

---

## Docker Daemon Configuration

```yaml id="ht3r44"
docker_manage_daemon_json: true
```

Controls deployment of:

```text id="yc4f22"
/etc/docker/daemon.json
```

---

## Logging Configuration

```yaml id="4ch1ry"
docker_daemon_log_driver: "json-file"

docker_daemon_log_max_size: "100m"

docker_daemon_log_max_file: "3"
```

Prevents uncontrolled log growth.

---

## Live Restore

```yaml id="v8a9d0"
docker_daemon_live_restore: true
```

Allows containers to continue running during Docker daemon restart.

Recommended for production systems.

---

# Daemon Configuration Template

Template file:

```text id="fgg3sl"
templates/daemon.json.j2
```

Example generated configuration:

```json id="12xjxg"
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m",
    "max-file": "3"
  },
  "storage-driver": "overlay2",
  "live-restore": true
}
```

---

# Verification Strategy

The role validates real runtime functionality.

Verification tasks include:

* `docker version`
* `docker info`
* `docker compose version`
* `docker buildx version`
* `systemctl status docker`

This prevents false-positive installations.

---

# Handlers

Defined in:

```text id="ukn0g9"
handlers/main.yml
```

Handlers include:

* Restart Docker
* Update APT cache
* Future daemon reload operations

---

# Example Playbook

## Install Docker

```yaml id="vqzk1k"
---
- name: Install Docker
  hosts: all
  become: true

  vars:
    docker_state: present

  roles:
    - role: docker_ce
```

---

## Uninstall Docker

```yaml id="wxg5nq"
---
- name: Uninstall Docker
  hosts: all
  become: true

  vars:
    docker_state: absent

  roles:
    - role: docker_ce
```

---

# Example Inventory Variables

```yaml id="d8f7od"
docker_version: "5:28.0.1-1~ubuntu.24.04~noble"

docker_remove_data: false

docker_enable_service: true

docker_start_service: true
```

---

# Supported Architectures

```yaml id="k6guk3"
docker_arch_map:
  x86_64: amd64
  aarch64: arm64
```

---

# Idempotency

This role is fully idempotent.

Repeated executions:

* do not reinstall unnecessarily
* do not duplicate repositories
* do not recreate unchanged configs
* safely converge to desired state

---

# Safe-by-Default Philosophy

This role intentionally avoids dangerous defaults.

Examples:

* No automatic runtime data deletion
* No deprecated repository trust methods
* No forced daemon wipes
* No unsafe package installation shortcuts

Production infrastructure should always prioritize:

* predictability
* rollback safety
* reproducibility
* operational stability

---

# Molecule Testing

Molecule structure is included for future automated role testing.

Location:

```text id="86m0mk"
molecule/default/
```

Supports future:

* CI validation
* idempotency testing
* multi-platform testing
* regression prevention

---

# Recommended Future Improvements

Possible future enhancements:

* Rootless Docker support
* SELinux integration
* AppArmor profiles
* CIS hardening
* Air-gapped repository support
* Private registry authentication
* TLS daemon configuration
* Kubernetes integration
* Swarm initialization
* Registry mirrors
* Metrics exporters
* Observability integrations

---

# Requirements

## Ansible

Recommended:

```text id="89gqif"
ansible-core >= 2.15
```

## Python

Recommended:

```text id="c1kz2g"
Python >= 3.10
```

---

# Design Principles

This role follows several engineering principles:

## Production-first

Designed for real infrastructure environments.

## Verification-driven

Validates runtime behavior, not only package presence.

## Safe-by-default

Dangerous operations require explicit enablement.

## Extensible

Supports future installation methods and runtime features.

## Reusable

Can be used across multiple inventories and environments.

## Environment-agnostic

Supports local systems, servers, VMs, and CI runners.

---

# License

Internal infrastructure automation role.

```
```
