Role Name
=========

Configuration mysql database

Role Variables
--------------

Create next group_vars file **USE ANSIBLE VAULT**
```yaml
---
ansible_user                : <username>
ansible_ssh_private_key_file: <path to ssh key>

MYSQL_ROOT_LOGIN   : <login>
MYSQL_ROOT_PASSWORD: <passwork>

DBS_CRED:
  - { user: <db name>,   password: "<db pass>", state: present }
```

Example Playbook
----------------

```yaml
- name: Configure mysql
  hosts: MYSQL
  become: true

  roles:
  - role: configure_mysql
```

Example Hosts
----------------
```
[MYSQL]
52.58.195.204
```

Example Comand
----------------
```sh
ansible-playbook configure-mysql.yaml --ask-vault-pass
```