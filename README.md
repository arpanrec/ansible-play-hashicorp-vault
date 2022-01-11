Ansible Playbook: hashicorp_vault
=================================

An ansible Playbook to install [HashiCorp Vault](https://www.vaultproject.io) with file backend on [docker](https://hub.docker.com/_/vault).

Playbook Variables
------------------

Playbook Variables

```yaml
- name: rv_hashicorp_vault_access_hostname
  description: The hostname of the Vault server, a TLS certificate will be issued to this name.
  required: no
  default: localhost
  type: str
- name: rv_hashicorp_vault_os_data_directory
  description: The directory where Vault data is stored.
  required: no
  default: /vault
  type: str
```

If you want to use a global dns entry in Godaddy

```yaml
- name: rv_hashicorp_vault_godaddy_entry
  description: The godaddy entry to use for the Vault server.
  required: no
  type: boolean
  default: false
  required_vars:
    - name: rv_hashicorp_vault_godaddy_api_key
      description: API key for updating godaddy entry.
      required: yes
      type: str
    - name: rv_hashicorp_vault_godaddy_api_secret
      description: API Secret for updating godaddy entry.
      required: yes
      type: str
    - name: rv_hashicorp_vault_godaddy_domain
      description: GO Daddy top domain.
      required: yes
      type: str
    - name: rv_hashicorp_vault_godaddy_hostname
      description: godaddy host for A record.
      required: yes
      type: str
```

Post Install check if vault is up

```yaml
- name: rv_hashicorp_vault_post_install_action_api_addr
  description: The address of the Vault server from native host.
  required: no
  default: https://localhost:8200
  type: str
```

Post install initialize vault server.

```yaml
- name: rv_hashicorp_vault_post_install_action_initialize
  description: Initialize the Vault server if not already initialized.
  required: no
  default: no
  type: boolean
  required_vars:
    - name: rv_hashicorp_vault_post_install_action_initialize_share
      description: "Number of key shares."
      required: no
      default: 5
      type: int
    - name: rv_hashicorp_vault_post_install_action_initialize_threshold
      description: "Number of key minimum shares."
      required: no
      default: 3
      type: int
    - name: rv_hashicorp_vault_post_install_action_initialize_secrets_directory
      description: "Root Token and unseal keys will be stored in this directory."
      required: no
      default: "{{  rv_hashicorp_vault_os_data_directory  }}/init_secrets"
      type: str
```

Setup Basic cron based auto-unseal

```yaml
- name: rv_hashicorp_vault_post_install_action_setup_auto_unseal_cron
  description: Setup cron job to run auto-unseal.
  required: no
  default: no
  type: boolean
  required_vars:
    - name: rv_hashicorp_vault_post_install_action_setup_auto_unseal_cron_dir
      description: "Cron Script and unseal keys will be stored here"
      required: no
      default: "{{ rv_hashicorp_vault_os_data_directory }}/unseal_corn"
      type: str
    - name: rv_hashicorp_vault_post_install_action_setup_unseal_keys
      description: "Vault unseal keys"
      required: yes
      type: list[str]
```

Dependencies
------------

```yaml
---
- src: https://github.com/sourceshift/ansible-role-linux-patching.git
  scm: git
  name: sourceshift.linux_patching

- src: https://github.com/geerlingguy/ansible-role-docker.git
  scm: git
  name: geerlingguy.docker

- src: https://github.com/sourceshift/ansible-role-utils.git
  scm: git
  name: sourceshift.utils

- src: https://github.com/sourceshift/ansible-role-ssh-hardening.git
  scm: git
  name: sourceshift.ssh_hardening

- src: https://github.com/sourceshift/ansible-role-user-add.git
  scm: git
  name: sourceshift.user_add

- src: https://github.com/sourceshift/ansible-role-server-workspace.git
  scm: git
  name: sourceshift.server_workspace
```

How to Run
----------

Clone source code and go to the source directory.

```bash
git clone --depth=1 https://github.com/sourceshift/ansible-play-hashicorp-vault.git
cd ansible-play-hashicorp-vault
```

Run the below script while in the source directory to install ansible playbook

```bash
pip install --user --upgrade virtualenv
virtualenv venv
source venv/bin/activate
ansible-galaxy install -r roles/requirements.yml
ansible-playbook site.yml --extra-vars "rv_hashicorp_vault_post_install_action_initialize=true rv_hashicorp_vault_post_install_action_setup_auto_unseal_cron=true"
```

License
-------

MIT
