.PHONY: \

#	create-ssh-keys \

	install-opencode-local uninstall-opencode-local \
	install-docker-local uninstall-docker-local \

	install-ssh-keys-work \
	
	install-ssh-keys-staging \
	install-opencode-staging uninstall-opencode-staging \
	install-docker-staging uninstall-docker-staging \

	install-ssh-keys-prod \
	install-opencode-prod uninstall-opencode-prod \
	install-docker-prod uninstall-docker-prod

# CREATE_SSH_KEYS=

LOCALHOST_INVENTORY=inventories/localhost/hosts

BOOTSTRAP_INVENTORY_WORK=inventories/bootstrap_work/hosts
BOOTSTRAP_INVENTORY_STAGING=inventories/bootstrap_staging/hosts
BOOTSTRAP_INVENTORY_PRODUCTION=inventories/bootstrap_production/hosts

STAGING_INVENTORY=inventories/staging/hosts
PRODUCTION_INVENTORY=inventories/production/hosts

BOOTSTRAP_SSH_KEYS_PLAYBOOK=playbooks/bootstrap/bootstrap_ssh_keys.yml

INSTALL_OPENCODE_PLAYBOOK=playbooks/opencode/install_opencode.yml
UNINSTALL_OPENCODE_PLAYBOOK=playbooks/opencode/uninstall_opencode.yml

INSTALL_DOCKER_PLAYBOOK=playbooks/docker/install_docker.yml
UNINSTALL_DOCKER_PLAYBOOK=playbooks/docker/uninstall_docker.yml

# create-ssh-keys:
# 	ansible-playbook -i 

# LOCALHOST
install-opencode-local:
	ansible-playbook --ask-become-pass -i $(LOCALHOST_INVENTORY) $(INSTALL_OPENCODE_PLAYBOOK)

uninstall-opencode-local:
	ansible-playbook --ask-become-pass -i $(LOCALHOST_INVENTORY) $(UNINSTALL_OPENCODE_PLAYBOOK)

install-docker-local:
	ansible-playbook --ask-become-pass -i $(LOCALHOST_INVENTORY) $(INSTALL_DOCKER_PLAYBOOK)

uninstall-docker-local:
	ansible-playbook --ask-become-pass -i $(LOCALHOST_INVENTORY) $(UNINSTALL_DOCKER_PLAYBOOK)


# WORK LAPTOP
install-ssh-keys-work:
	ansible-playbook -i $(BOOTSTRAP_INVENTORY_WORK) $(BOOTSTRAP_SSH_KEYS_PLAYBOOK)


# STAGING
install-ssh-keys-staging:
	ansible-playbook -i $(BOOTSTRAP_INVENTORY_STAGING) $(BOOTSTRAP_SSH_KEYS_PLAYBOOK)

install-opencode-staging:
	ansible-playbook -i $(STAGING_INVENTORY) $(INSTALL_OPENCODE_PLAYBOOK)

uninstall-opencode-staging:
	ansible-playbook -i $(STAGING_INVENTORY) $(UNINSTALL_OPENCODE_PLAYBOOK)

install-docker-staging:
	ansible-playbook -i $(STAGING_INVENTORY) $(INSTALL_DOCKER_PLAYBOOK)

uninstall-docker-staging:
	ansible-playbook -i $(STAGING_INVENTORY) $(UNINSTALL_DOCKER_PLAYBOOK)


# PRODUCTION
install-ssh-keys-prod:
	ansible-playbook -i $(PRODUCTION_INVENTORY_PRODUCTION) $(BOOTSTRAP_SSH_KEYS_PLAYBOOK)

install-opencode-prod:
	ansible-playbook -i $(PRODUCTION_INVENTORY) $(INSTALL_OPENCODE_PLAYBOOK)

uninstall-opencode-prod:
	ansible-playbook -i $(PRODUCTION_INVENTORY) $(UNINSTALL_OPENCODE_PLAYBOOK)

install-docker-prod:
	ansible-playbook -i $(PRODUCTION_INVENTORY) $(INSTALL_DOCKER_PLAYBOOK)

uninstall-docker-prod:
	ansible-playbook -i $(PRODUCTION_INVENTORY) $(UNINSTALL_DOCKER_PLAYBOOK)

