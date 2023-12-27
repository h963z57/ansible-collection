Role Name
=========

Install & configure wireguard

Role Variables
--------------

Create next group_vars file **USE ANSIBLE VAULT**
```yaml
---
ansible_user                : <login>
ansible_ssh_private_key_file: <path to ssh key>

WIREGUARD_PRIVATE_KEY: <key>
WIREGUARD_IP         : "172.26.0.1"
WIREGUARD_PORT       : "13231"
WIREGUARD_ACCOUNTS:  # list of clients cred
    - {name: <name of acc>,   public_key: <pub_key>, ip: "172.26.0.2", target: <name of WIREGUARD_NODE var>, state: present}
    # Example
    - {name: <name of acc>,   public_key: <pub_key>, ip: "172.26.0.2", target: main, state: present}

```

Example Playbook
----------------

```yaml
- name: Configure wireguard
  hosts: WIREGUARD
  become: true

  roles:
  - role: wireguard
```

Example Hosts
----------------
```
[PROXY]
52.58.195.204 WIREGUARD_NODE=main
```

Example Comand
----------------
```sh
ansible-playbook configure_wireguard.yaml --ask-vault-pass
```