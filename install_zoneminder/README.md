Role Name
=========

Install zoneminder

Role Variables
--------------

Create next group_vars file **USE ANSIBLE VAULT**
```yaml
---
ansible_user                : <username>
ansible_ssh_private_key_file: <path to ssh key>
```

Example Playbook
----------------

```yaml
- name: Install zoneminder
  hosts: ZONEMINDER
  become: true

  roles:
  - role: install_zoneminder
```

Example Hosts
----------------
```
[ZONEMINDER]
52.58.195.204
```

Example Comand
----------------
```sh
ansible-playbook install_zoneminder.yaml
```