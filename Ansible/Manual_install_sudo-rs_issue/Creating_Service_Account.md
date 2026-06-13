# Creation of Service Account for Automation (Example: `ansible`)

## Goal

Separate interactive work from automation:

* **sakura** → human user
* **ansible** → automation user

Benefits:

* Separate SSH keys
* Separate home directories
* Separate `.ssh/known_hosts`
* Passwordless sudo only for automation
* Human account still protected by password
* Better auditability
* Ready for Ansible, AWX, Jenkins, GitHub Actions, etc.

---

# Architecture

## Home laptop

SSH keys:

```text
~/.ssh/work
~/.ssh/work.pub

~/.ssh/work_ansible
~/.ssh/work_ansible.pub
```

## Work laptop

Users:

```text
sakura
ansible
```

### sakura

* Interactive user
* Password required for sudo

### ansible

* Automation user
* Passwordless sudo

---

# Step 1. Create User

Login as your normal user:

```bash
ssh work
```

Create user:

```bash
sudo useradd -m -s /bin/bash ansible
```

Verify:

```bash
id ansible
```

Expected:

```text
uid=1001(ansible) gid=1001(ansible)
```

---

# Step 2. Add User to sudo Group

```bash
sudo usermod -aG sudo ansible
```

Verify:

```bash
id -nG ansible
```

Expected:

```text
ansible sudo
```

---

# Step 3. Create SSH Directory

Create:

```bash
sudo install -d -m 700 -o ansible -g ansible /home/ansible/.ssh
```

Verify:

```bash
ls -ld /home/ansible/.ssh --> Permission denied
sudo ls -ld /home/ansible/.ssh
```

Expected:

```text
drwx------ ansible ansible
```

---

# Step 4. Install SSH Public Key 

(manual or auto - but only after Ansible bootstrap: install-ssh-keys-work)

From home laptop:

```bash
cat ~/.ssh/work_ansible.pub
```

Copy the output.

Create:

```bash
sudo touch /home/ansible/.ssh/authorized_keys
```

Paste:

```text
ssh-ed25519 AAAA...
```

or (auto):

```text
sudo sh -c "sed -n '2p' /home/sakura/.ssh/authorized_keys >> /home/ansible/.ssh/authorized_keys"
```

Set ownership:

```bash
sudo chown ansible:ansible /home/ansible/.ssh/authorized_keys
```

Set permissions:

```bash
sudo chmod 600 /home/ansible/.ssh/authorized_keys
```

Verify:

```bash
ls -l /home/ansible/.ssh
```

Expected:

```text
-rw------- ansible ansible authorized_keys
```

---

# Step 5. Configure Passwordless sudo

Create:

```text
/etc/sudoers.d/ansible
```

Content:

```text
ansible ALL=(ALL:ALL) NOPASSWD:ALL
```

Create:

```bash
sudo tee /etc/sudoers.d/ansible >/dev/null <<EOF
ansible ALL=(ALL:ALL) NOPASSWD:ALL
EOF
```

Set permissions:

```bash
sudo chmod 440 /etc/sudoers.d/ansible
```

Validate:

```bash
sudo visudo -cf /etc/sudoers.d/ansible
```

Expected:

```text
parsed OK
```

---

# Step 6. Verify Passwordless sudo

Switch user:

```bash
sudo su - ansible
[sudo: authenticate] Password: {pass_from_sakura}
```

Run:

```bash
whoami
```

Expected:

```text
ansible
```

Run:

```bash
sudo whoami
```

Expected:

```text
root
```

No password should be requested.


---

# Step 7. Configure SSH Client on Home Laptop

Edit:

```text
~/.ssh/config
```

Add:

```text
# Home to Work Laptop
Host work
    HostName work.local
    User sakura
    IdentityFile ~/.ssh/work

# Home-Ansible to Work Laptop
Host work-ansible
    HostName work.local
    User ansible
    IdentityFile ~/.ssh/work_ansible
    IdentitiesOnly yes
```

---

# Step 8. Test SSH

Human access:

```bash
ssh work
```

Automation access:

```bash
ssh work-ansible
```

Verify:

```bash
whoami
```

Expected:

```text
ansible
```

Verify sudo:

```bash
sudo whoami
```

Expected:

```text
root
```

No password should be requested.


---

# Step 9. SSH Hardening

No additional SSH configuration is required.

Existing:

```text
/etc/ssh/sshd_config.d/99-hardening.conf
```

applies to all users:

# SSH Hardening Configuration

```text
PubkeyAuthentication yes
PasswordAuthentication no
KbdInteractiveAuthentication no
ChallengeResponseAuthentication no
PermitEmptyPasswords no
PermitRootLogin no
UsePAM yes
MaxAuthTries 3
```

| Setting                              | Description                                                                                      |
| ------------------------------------ | ------------------------------------------------------------------------------------------------ |
| `PubkeyAuthentication yes`           | Enable SSH public key authentication. Required for passwordless SSH access.                      |
| `PasswordAuthentication no`          | Disable password-based SSH login. Only SSH keys are accepted.                                    |
| `KbdInteractiveAuthentication no`    | Disable keyboard-interactive authentication (PAM prompts, OTP, etc.).                            |
| `ChallengeResponseAuthentication no` | Disable challenge-response authentication mechanisms.                                            |
| `PermitEmptyPasswords no`            | Prevent accounts with empty passwords from logging in.                                           |
| `PermitRootLogin no`                 | Disable direct SSH login as `root`. Users must use `sudo` after login.                           |
| `UsePAM yes`                         | Enable PAM for account and session management. Recommended even when password login is disabled. |
| `MaxAuthTries 3`                     | Limit authentication attempts to three before disconnecting, reducing brute-force attacks.       |

## Security Goals

* SSH key authentication only.
* No password-based SSH access.
* No direct root login.
* Protection against brute-force attacks.
* Secure account and session management through PAM.
* Suitable for homelab and DevSecOps environments.

No separate file for `ansible` is required.

---

# Step 10. Verify File Permissions

## SSH directory

```text
/home/ansible/.ssh
drwx------ (700)
```

Owner:

```text
ansible:ansible
```

## authorized_keys

```text
/home/ansible/.ssh/authorized_keys
-rw------- (600)
```

Owner:

```text
ansible:ansible
```

## sudoers file

```text
/etc/sudoers.d/ansible
-r--r----- (440)
```

Owner:

```text
root:root
```

---

# Final Architecture

Home laptop:

```text
~/.ssh/work
~/.ssh/work_ansible
```

Work laptop:

```text
sakura
│
├── password sudo
└── ~/.ssh/authorized_keys

ansible
│
├── NOPASSWD sudo
└── ~/.ssh/authorized_keys
```

SSH:

```text
ssh work
```

↓

```text
sakura
```

SSH:

```text
ssh work-ansible
```

↓

```text
ansible
```

Ansible:

```text
home
 ↓
work-ansible key
 ↓
ansible
 ↓
sudo
 ↓
root
```

This architecture cleanly separates human access from automation and scales well to future DevSecOps environments.