# Vagrant Ubuntu Homelab

Production-style Vagrant environment for creating one or more Ubuntu Server VMs on VirtualBox.

Designed for DevSecOps homelabs with:

* Ubuntu Server 26.04
* Headless VirtualBox VMs
* SSH key authentication only
* Host-only networking
* Passwordless sudo
* Avahi (.local hostname resolution)
* Optional Docker installation
* Idempotent provisioning
* Multi-VM support

---

# Requirements

* VirtualBox
* Vagrant
* SSH key pair(s)

Example:

```text
~/.ssh/home_srv1
~/.ssh/home_srv1.pub

~/.ssh/home_srv2
~/.ssh/home_srv2.pub
```

---

# Project Structure

```text
.
├── Vagrantfile
├── README.md
└── vagrant_cmd.md
```

`vagrant_cmd.md` contains common Vagrant commands and administration examples.

---

# Features

## Ubuntu 26.04

Uses:

```ruby
VM_BOX = "bento/ubuntu-26.04"
```

---

## Headless Virtual Machines

VirtualBox GUI is disabled:

```ruby
vb.gui = false
```

VMs run entirely in the background.

---

## SSH Key Authentication Only

Password authentication is disabled.

Enabled:

* PubkeyAuthentication

Disabled:

* PasswordAuthentication
* Root login
* Keyboard interactive authentication

SSH configuration:

```text
PasswordAuthentication no
PermitRootLogin no
PubkeyAuthentication yes
KbdInteractiveAuthentication no
PermitEmptyPasswords no
UsePAM yes
X11Forwarding no
MaxAuthTries 3
```

---

## Passwordless sudo

User receives:

```text
NOPASSWD:ALL
```

No password is required when using:

```bash
sudo apt update
sudo systemctl status ssh
```

---

## Avahi Support

Installs:

* avahi-daemon

Allows hostname resolution using:

```text
hsrv1.local (192.168.56.11)
hsrv2.local
```

Example:

```bash
ssh sakura@hsrv1.local 

(using ssh config:
Host hsrv1
    HostName 192.168.56.11
    User sakura
    IdentityFile ~/.ssh/home_srv1)
ssh hsrv1
```

---

## Optional Docker Installation

Docker installation can be enabled with:

```ruby
INSTALL_DOCKER = true
```

Installs:

* docker-ce
* docker-ce-cli
* containerd.io
* docker-buildx-plugin
* docker-compose-plugin

Docker service is automatically enabled.

---

# Multi-VM Support

Number of VMs:

```ruby
VM_COUNT = 1
```

Increasing:

```ruby
VM_COUNT = 3
```

creates:

```text
homeVM1
homeVM2
homeVM3
```

with hostnames:

```text
hsrv1
hsrv2
hsrv3
```

and IP addresses:

```text
192.168.56.11
192.168.56.12
192.168.56.13
```

---

# Configuration

## VM Name Prefix

```ruby
VM_PREFIX = "homeVM"
```

Creates:

```text
homeVM1
homeVM2
...
```

---

## Hostname Prefix

```ruby
HOST_PREFIX = "hsrv"
```

Creates:

```text
hsrv1
hsrv2
...
```

---

## Memory

```ruby
VM_MEMORY = 2048
```

Example:

```ruby
VM_MEMORY = 4096
```

---

## CPUs

```ruby
VM_CPUS = 2
```

Example:

```ruby
VM_CPUS = 4
```

---

## User

```ruby
USER_NAME = "sakura"
```

Default login:

```bash
ssh sakura@hsrv1.local
```

---

## Network

Uses:

* Adapter 1: NAT
* Adapter 2: Host-only

Default network:

```ruby
BASE_IP = "192.168.56."
BASE_IP_LAST_OCTET = 10
```

Result:

```text
VM1 → 192.168.56.11
VM2 → 192.168.56.12
VM3 → 192.168.56.13
```

---

# SSH Keys

Keys are searched in:

```ruby
SSH_KEY_DIR = "~/.ssh"
SSH_KEY_PREFIX = "home_srv"
```

For VM_COUNT = 3:

Required:

```text
~/.ssh/home_srv1.pub
~/.ssh/home_srv2.pub
~/.ssh/home_srv3.pub
```

Provisioning stops if a public key is missing.

---

# Installed Packages

Base packages:

* openssh-server
* rsync
* git
* htop
* avahi-daemon
* net-tools
* iputils-ping
* dnsutils
* curl
* wget
* nano
* vim
* tree
* ca-certificates
* bash-completion

---

# Example Connections

By IP:

```bash
ssh sakura@192.168.56.11
```

By hostname:

```bash
ssh sakura@hsrv1.local
```

Using a specific key:

```bash
ssh -i ~/.ssh/home_srv1 sakura@hsrv1.local

By short name:
ssh hsrv1
```

---

# Rebuild Environment

Destroy:

```bash
vagrant destroy -f
```

Create:

```bash
vagrant up
```

Re-provision:

```bash
vagrant provision
```

---

# Common Commands

See:

```text
vagrant_cmd.md
```

for:

* vagrant up
* vagrant halt
* vagrant reload
* vagrant destroy
* vagrant provision
* ssh examples
* VirtualBox commands
* troubleshooting

---

# Intended Use

This project is intended for:

* DevSecOps labs
* Docker testing
* Ansible testing
* Kubernetes learning
* CI/CD experiments
* Networking practice
* Linux administration
* Disposable infrastructure

The environment is reproducible, lightweight, and suitable for creating multiple Ubuntu Server VMs with minimal manual configuration.

