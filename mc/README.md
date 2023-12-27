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
  - {name: s3, url: "https://endpoint.com", access_key: "access_key", secret_key: "secret_key", target: <name of MC_NODE var>, state: present}
  # Example
  - {name: s3, url: "https://endpoint.com", access_key: "access_key", secret_key: "secret_key", target: main, state: present}
MC_BACKUP_CONFIGS:
  # Backup from local to s3
  - {name: "Unique name 1", comman: "mc mirror --remove --overwrite /my/path/ s3/my-bucket/", month: "*", day: "*", weekday: "6", hour: "3", minute: "0", target: main, state: present}
  # Backup from s3 to s3
  - {name: "Unique name 2", comman: "mc mirror --remove --overwrite s3/my-bucket-1/ s3/my-bucket-2/", month: "*", day: "15", weekday: "*", hour: "3", minute: "0", target: second, state: present}

  # if you need special time
  - {name: , comman: "mc mirror ... ", special_time: "reboot", target: main, state: absent}
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
52.58.195.204 MC_NODE=main
52.58.195.205 MC_NODE=second
```

Example Comand
----------------
```sh
# First start
ansible-playbook configure_mc_backup.yaml -e MC_INSTALL=true --ask-vault-pass

# Configure (without installation)
ansible-playbook configure_mc_backup.yaml --ask-vault-pass
```