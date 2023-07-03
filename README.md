# Ansible roles

## Role usage

### File structure
```
├── _files 
│   ├── configs 
│   ├── ssh-keys
│   ├── secrets
│   ├── tmp
├── _group_vars
│   ├── EXAMPLE
│   └── EXAMPLE
├─── ansible.cfg
├─── hosts.txt
├─── playbook_example_1.yaml
└─── playbook_example_2.yaml
```

### File structure discription

|  File  | Description |
| ------ | ----------- |
| files   | store files/rsa-pub/secrets (**recommend use encrypted group vars for sencitive vars**)/etc  |
| ansible.cfg | ansible settings |
|  group_vars  | store role options,  *see role readme*

### ansible.cfg example
```
[defaults]
host_key_checking       = false
inventory               = ./hosts.txt
# fact_caching_connection = ../../var/cache
# log_path                = ../../var/log/ansible.log
# retry_files_save_path   = ../../var/retry
```
