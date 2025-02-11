3X-UI manager
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
- name: Login to 3x-ui
  hosts: VPN
  gather_facts: no  
  connection: local

  vars:
    UPDATE: false
    STATUS: true

  roles:
  - role: 3x-ui
```

Example Hosts
----------------
```
[VPN]
52.58.195.204
```

Example vars
----------------
```yaml
xui_host: "{{inventory_hostname}}"
xui_port: 60443

xui_username: "<login>"
xui_password: "<password>"

VPN_CLIENTS:
  - {name: vpn-1, id: 1, enabled: "true", port: 30087, region: vpn-gen-1.example.net, 
    publicKey: "2OZTDFZ4MARuYl7kteQtODqb6aLGkCwvdUm9dcYakm4",
    privateKey: "2IeHlZoyhcg-hfXorXiUCdIRpgXkWpxfj0GVi-vCJ0w",
    shortID: "40a799c7",
    clients: [
      {email: noname, enabled: "true", expiryTime: 1740776400854, id: fad8e324-ad2c-4f6b-982b-f57d7ec002a7, totalGB: 100 , tgid: ""},
    ]} 

  # - {name: vpn-?, id: ??, enabled: "true", port: 300??, region: vpn-fra-1.h963z57.net, 
  #   publicKey: "",
  #   privateKey: "",
  #   shortID: "",
  #   clients: [
  #     {email: noname, enabled: "true", expiryTime: 1740776400854, id: ???, totalGB: 100 , tgid: ""},
  #   ]} 
```


Example Comand
----------------
```sh
ansible-playbook configure-vpn.yaml --ask-vault-pass
```