.PHONY: \
	install-opencode-prod install-opencode-staging \
	uninstall-opencode-prod uninstall-opencode-staging \
	install-docker-prod install-docker-staging \
	uninstall-docker-prod uninstall-docker-staging

INSTALL_OPENCODE_PLAYBOOK=playbooks/opencode/install_opencode.yml
UNINSTALL_OPENCODE_PLAYBOOK=playbooks/opencode/uninstall_opencode.yml

INSTALL_DOCKER_PLAYBOOK=playbooks/docker/install_docker.yml
UNINSTALL_DOCKER_PLAYBOOK=playbooks/docker/uninstall_docker.yml

install-opencode-prod:
	ansible-playbook -i inventories/production/hosts $(INSTALL_OPENCODE_PLAYBOOK)

install-opencode-staging:
	ansible-playbook -i inventories/staging/hosts $(INSTALL_OPENCODE_PLAYBOOK)

uninstall-opencode-prod:
	ansible-playbook -i inventories/production/hosts $(UNINSTALL_OPENCODE_PLAYBOOK)

uninstall-opencode-staging:
	ansible-playbook -i inventories/staging/hosts $(UNINSTALL_OPENCODE_PLAYBOOK)

install-docker-prod:
	ansible-playbook -i inventories/production/hosts $(INSTALL_DOCKER_PLAYBOOK)

install-docker-staging:
	ansible-playbook -i inventories/staging/hosts $(INSTALL_DOCKER_PLAYBOOK)

uninstall-docker-prod:
	ansible-playbook -i inventories/production/hosts $(UNINSTALL_DOCKER_PLAYBOOK)

uninstall-docker-staging:
	ansible-playbook -i inventories/staging/hosts $(UNINSTALL_DOCKER_PLAYBOOK)