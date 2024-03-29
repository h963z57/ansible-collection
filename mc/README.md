Role Name
=========

Install & configure mc (minio) for backup to s3 storage

Discription
--------------
Role using for S3 minio application and crontab for shedule. See mc and crontab docs for more info

Role Variables
--------------

Create next group_vars file **USE ANSIBLE VAULT**
```yaml
---
ansible_user                : <you login>
ansible_ssh_private_key_file: <path to ssh key>

MC_INSTALL: false # chage to true for first time
# NAMES MUST BE DIFFERENT
MC_CREDENTIALS:   # list of mc credentials
  - {name: s3, url: "https://endpoint.com", access_key: "access_key", secret_key: "secret_key", target: <name of SERVERNAME var>, state: present}
  # Example
  - {name: s3, url: "https://endpoint.com", access_key: "access_key", secret_key: "secret_key", target: master, state: present}
MC_SYNCPAIR:
  - {name: "NAME(of db)", src: "127.0.0.1(or path)", dest: "s3/bucket-name/some_dir(dir is optional)", target: master,  type: "pgsql (may be file, tar, synk)", state: present} 


```

Example Playbook
----------------

```yaml
- name: Configure mc backup
  hosts: BACKUPS
  become: true

  roles:
    - mc
```

Example Hosts
----------------
```
[PROXY]
52.58.195.204 SERVERNAME=master MC_NODE=true
52.58.195.205 SERVERNAME=slave MC_NODE=true
```

Example Comand
----------------
```sh
# First start
ansible-playbook configure_mc_backup.yaml -e MC_INSTALL=true --ask-vault-pass

# Configure (without installation)
ansible-playbook configure_mc_backup.yaml --ask-vault-pass
```