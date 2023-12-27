Role Name
=========

Install & configure 3proxy


Role Variables
--------------

Create next group_vars file **USE ANSIBLE VAULT**
```yaml
---
ansible_user                : <username>
ansible_ssh_private_key_file: <path to ssh key>

PROXY_INSTALL: false # chage to true for first time
PROXY_PORT: 3128     # default 3proxy port
PROXY_NAME_SERVERS:  # list of dns serves
  - 8.8.8.8
  - 8.8.4.4
PROXY_ACCOUNTS:      # list of proxy credentials
  - {name: <your login>, password: "<your pass>", pass_type: <type of password see 3proxy doc default is CL (unencrypted)>, target: <name of PROXY_NODE var>, state: present}
  # example
  - {name: login, password: "pass", pass_type: CL, target: proxy-0, state: present}
```

Example Playbook
----------------

```yaml
- name: Configure 3proxy
  hosts: PROXY
  become: true

  roles:
  - role: 3proxy
```

Example Hosts
----------------
```
[PROXY]
52.58.195.204 PROXY_NODE=proxy-0
```

Example Comand
----------------
```sh
# First start
ansible-playbook configure-proxy.yaml -e PROXY_INSTALL=true --ask-vault-pass

# Configure (without installation)
ansible-playbook configure-proxy.yaml --ask-vault-pass
```