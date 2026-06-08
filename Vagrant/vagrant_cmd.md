# Vagrant Commands Cheat Sheet

## Initialization

### Create a new Vagrantfile

```bash
vagrant init
```

### Create a Vagrantfile with a specific box

```bash
vagrant init bento/ubuntu-26.04
```

### Validate the Vagrantfile syntax

```bash
vagrant validate
```

---

## Create and Start

### Create and start all VMs

```bash
vagrant up
```

### Create and start a single VM

```bash
vagrant up homeVM1
```

---

## SSH Access

### Connect using Vagrant SSH

```bash
vagrant ssh homeVM1
```

### Show SSH configuration

```bash
vagrant ssh-config
```

### Connect using custom SSH aliases

```bash
ssh hsrv1
ssh hsrv2
ssh hsrv3
```

---

## Status

### Show VM status

```bash
vagrant status
```

### Show all Vagrant environments

```bash
vagrant global-status
```

---

## Reload and Provisioning

### Restart VMs

```bash
vagrant reload
```

### Restart and re-run provisioning

```bash
vagrant reload --provision
```

### Run provisioning

```bash
vagrant provision
```

### Run provisioning for a single VM

```bash
vagrant provision homeVM1
```

---

## Stop and Resume

### Gracefully stop all VMs

```bash
vagrant halt
```

### Stop one VM

```bash
vagrant halt homeVM1
```

### Suspend VMs

```bash
vagrant suspend
```

### Resume VMs

```bash
vagrant resume
```

### Resume a single VM

```bash
vagrant resume homeVM1
```

---

## Destroy

### Delete all VMs

```bash
vagrant destroy
```

### Force delete without confirmation

```bash
vagrant destroy -f
```

### Delete one VM

```bash
vagrant destroy homeVM1
```

---

## Boxes

### List installed boxes

```bash
vagrant box list
```

### Download and add a box

```bash
vagrant box add bento/ubuntu-26.04
```

### Update a box

```bash
vagrant box update
```

### Remove a box

```bash
vagrant box remove bento/ubuntu-26.04
```

---

## Plugins

### List installed plugins

```bash
vagrant plugin list
```

### Install a plugin

```bash
vagrant plugin install <plugin>
```

### Remove a plugin

```bash
vagrant plugin uninstall <plugin>
```

### Update plugins

```bash
vagrant plugin update
```

---

## Snapshots

### Create a snapshot

```bash
vagrant snapshot save NAME
```

### Restore a snapshot

```bash
vagrant snapshot restore NAME
```

### List snapshots

```bash
vagrant snapshot list
```

### Delete a snapshot

```bash
vagrant snapshot delete NAME
```

### Push current state

```bash
vagrant snapshot push
```

### Restore previous pushed state

```bash
vagrant snapshot pop
```

---

## Packaging

### Package a VM into a box

```bash
vagrant package
```

---

## Information

### Show Vagrant version

```bash
vagrant version
```

### Display help

```bash
vagrant help
```

### Show available subcommands

```bash
vagrant list-commands
```

---

## Recommended Daily Workflow

### Start VMs

```bash
vagrant up
```

### Connect

```bash
ssh hsrv1
```

### Check status

```bash
vagrant status
```

### Apply Vagrantfile changes

```bash
vagrant reload
```

### Re-run provisioning

```bash
vagrant provision
```

### Stop VMs

```bash
vagrant halt
```

### Delete VMs

```bash
vagrant destroy
```
