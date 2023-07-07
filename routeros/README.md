RouterOS
=========

Role for configuration RouterOS devices. 

Functions:
-----------
- Init new device
- Configuration firewall
  - Add/remove allow rule for establised/related/untracked frames input/forward chain
  - Add/remove block rule for invalid package input/forward chain
  - PSD check input/forward chain
  - Add/remove allow rule for dns/ntp addresses.
  - Add/remove **disabled** deny all rules input/forward chain
- User controll (add/remove).
- Enable/disable services (www, telnet, etc)
- Configure bridge (add/remove). This func make next steps:
  - Create bridge
  - Create addres (/ip address) for bridge
  - Create ip pool
  - Create network for bridge
  - Create DHCP server for bridge
  - Add ip pool into input/exeption list for firewall
- Configure DNS and add addresses into input/exeption list for firewall
- Configure hostname
- Add/remove interface lists
- Enable/disable IPv6
- Configure time settings:
  - Set time-zone
  - Enable NTP client
  - Add/Remove NTP servers
  - Add/Remove in Input/Exeption lists.
  - Enable/disable NTP servers
- Configure WireGuard connection (client mode)
- Configure CAPsMAN:
  - Enable/Disable controller
  - Create security profile
  - Create datapath profile
  - Create channel profile
  - Create configuration
  - Create provisioning

Requirements
------------

**apt-get install pwgen** on Ansible host

Role Usage
--------------

1) Create file structure as in root readme
2) Create hosts.txt file 

``` yaml
[ALL]
192.168.30.244

[INIT]
192.168.30.250
192.168.30.238

[COMMON_SETTINGS]
192.168.30.250 MIKROTIK_HOSTNAME=test1 MIKROTIK_POOL="10.1"
192.168.30.238 MIKROTIK_HOSTNAME=test2 MIKROTIK_POOL="10.2"

```

|  Group           | Variables | Description |
| ---------------- | ----------- | --------|
| ALL              | - | May use for apply common settings as time, dns, users, enable/disable services|
| INIT             | - | Create ansible user with ssh-key                                              |
| COMMON_SETTINGS  | MIKROTIK_HOSTNAME | Unique name, also use wireguard configuration                 |
| COMMON_SETTINGS  | MIKROTIK_POOL     | Use for create dhcp                                           |
 
3) Create playbook and chose function. For use func, you need type true in vars.

Init new device

```yaml
---
- hosts: INIT  
  gather_facts: no  
  connection: local

  vars:
    MIKROTIK_INIT: true

  vars_prompt:
  - name: "ros_login"
    prompt: "Enter login"
    private: no
  
  - name: "ros_passwd"
    prompt: "Enter password"
    private: yes

  roles:
  - routeros
```
Configure routers
```yaml
---
- hosts: ROUTERS  
  gather_facts: no  
  connection: local

  vars:
    MIKROTIK_USER_CONTROL: true
    MIKROTIK_SERVICE_CONTROL: true
    MIKROTIK_HOSTNAME_CONTROL: true
    MIKROTIK_TIME_CONTROL: true
    MIKROTIK_DNS_CONTROL: true
    MIKROTIK_IPV6_CONTROL: true
    MIKROTIK_INTERFACE_LISTS_CONTROL: true
    MIKROTIK_ACCESS_CONTROL: true
    MIKROTIK_BRIDGE_CONTROL: true
    MIKROTIK_CAPsMAN: true
    MIKROTIK_WIREGUARD_CONTROL: true
    MIKROTIK_FIREWALL_CONTROL: true

  roles:
  - routeros
```
Configure switch
```yaml
---
- hosts: SWITCH  
  gather_facts: no  
  connection: local

  vars:
    MIKROTIK_USER_CONTROL: true
    MIKROTIK_SERVICE_CONTROL: true
    MIKROTIK_HOSTNAME_CONTROL: true
    MIKROTIK_TIME_CONTROL: true
    MIKROTIK_DNS_CONTROL: true
    MIKROTIK_IPV6_CONTROL: true
    MIKROTIK_ACCESS_CONTROL: true
    MIKROTIK_CAPsMAN: true

  roles:
  - routeros
```
4) Create group_vars with settings. **Encrypt this file**

INIT group_vars example
```yaml
---
MIKROTIK_ANSIBLE_ADMIN: "ansible_admin"
MIKROTIK_ANSIBLE_SSH_PUB: "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCyEFDyPIcq4/pOPC3c1DJUFoZUoU ..."
```

Configuration group_vars example
```yaml
---
# ====================== Credentials ==========================
ansible_user                : ansible_cloud
ansible_ssh_private_key_file: <path to private key file>
# ====================== Credentials ==========================

## MIKROTIK_PING_CHECK: "8.8.8.8" # Does't work func

MIKROTIK_USERS:
    - {name: user_1, password: "1234", group: full, state: present, comment: "Added by Ansible"}
    - {name: user_2, password: "1234", group: read, state: absent,  comment: "Added by Ansible"}

MIKROTIK_SERVICES:
    - {name: telnet, disabled: "yes"}
    - {name: ftp,    disabled: "yes"}
    - {name: api,    disabled: "yes"}
    - {name: api-ssl,disabled: "yes"}
    - {name: www,    disabled: "yes"}
    - {name: www-ssl,disabled: "yes"}
    - {name: ssh,    disabled: "no"}
    - {name: winbox, disabled: "no"}

MIKROTIK_TIMEZONE: Europe/Moscow

MIKROTIK_NTP:
    SERVERS:
        - {addr: time.google.com, state: present, comment: "Added by Ansible"}
        - {addr: 0.pool.ntp.org,  state: absent, comment: "Added by Ansible"}
        - {addr: 1.pool.ntp.org,  state: present, comment: "Added by Ansible"}
        - {addr: 2.pool.ntp.org,  state: absent, comment: "Added by Ansible"}
        - {addr: 3.pool.ntp.org,  state: present, comment: "Added by Ansible"}
    CREATE_ACCESS_FIREWALL_RULE: true  # Add record into Input/Exeption list
    ENABLE_SERVER              : "yes" # Allow clients read NTP data
 
MIKROTIK_DNS:
    SERVERS:
        - 8.8.8.8
        - 8.8.4.4
        - 1.1.1.1
    ALLOW_REMOTE_REQUESTS      : "yes" # Allow clients read dns records
    CREATE_ACCESS_FIREWALL_RULE: true  # Add record into Input/Exeption list

MIKROTIK_IPV6:
    STATE: false

MIKROTIK_INTERFACE_LISTS:
    - {name: WAN, state: present}
    - {name: LAN, state: present}

MIKROTIK_ACCESS_POLICY: 
    NEIGHBOR  : "!WAN"
    MAC_WINBOX: "LAN"

MIKROTIK_BRIDGE:
    LISTS:
        - {name: bridge1, state: present, subnet: "1", dhcp_start: "200", arp_policy: "enabled",    arp_dhcp: "no", comment: "Added by Ansible"}
        - {name: bridge2, state: present, subnet: "2", dhcp_start: "200", arp_policy: "enabled",    arp_dhcp: "no", comment: "Added by Ansible"}
        - {name: bridge3, state: present, subnet: "3", dhcp_start: "100", arp_policy: "enabled",    arp_dhcp: "no", comment: "Added by Ansible"}
        - {name: bridge4, state: present, subnet: "4", dhcp_start: "81",  arp_policy: "reply-only", arp_dhcp: "yes",comment: "Added by Ansible"}
        - {name: bridge5, state: present, subnet: "5", dhcp_start: "81",  arp_policy: "reply-only", arp_dhcp: "yes",comment: "Added by Ansible"}

MIKROTIK_WIREGUARD:
    - {name: WG_to_cloud, private_key: "0PS5hNrrhbKMwgKqWEFTAmM/M5lo=", public_key: "KMTDOMfoHkkxotcC2HU5Lfv5BA=", entrypoint: "x.x.x.x", port: "51820", allowed_addr: "172.0.0.0/8", ip: "172.15.1.2", target: "<Router hostname 1>", comment: "Connect my cloud", state: present}
    - {name: WG_to_cloud, private_key: "/vdQN9lHEop9ZU7EFTAmM/M5lo=", public_key: "KMTZCeOMfoHkkxotcC2HU5Lfv5BA=", entrypoint: "x.x.x.x", port: "51820", allowed_addr: "172.0.0.0/8", ip: "172.15.1.3", target: "<Router hostname 2>", comment: "Connect my work", state: present}

MIKROTIK_FIREWALL:
    STATE: true
    RULES:
        BASIK_ALLOW: true
        BASIK_BLOCK: true
        PSD_CHECK: true
        ALLOW_DNS: true
        ALLOW_NTP: true
        DELY_ALL: true
        DDOS_PROTECTION: false ##EXPEREMENTAL

MIKROTIK_CAPSMAN:
    SERVER:
        STATE: true
        SECURITY:
            - {name: security, passphrase: "MyPass", authentication_types: "wpa2-psk", encryption: "aes-ccm", group_encryption: "aes-ccm", group_key_update: "40m", target: test1, state: present, comment: "Added by Ansible"}
        DATAPATHS:
            - {name: datapath, bridge: bridge-vlan4, client_to_client_forwarding: "yes", local_forwarding: "no", target: test1, state: present, comment: "Added by Ansible"}
        CHANNELS:
            - {name: "24Ghz", band: "2ghz-onlyn", control_channel_width: 20mhz, frequency: "2412,2437,2462", reselect_interval: "1h", tx_power: "14",                                                      target: test1, state: present, comment: "Added by Ansible"}
            - {name: "5Ghz",  band: "5ghz-n/ac",  control_channel_width: 20mhz, frequency: "",               reselect_interval: "1h", tx_power: "20", extension_channel: "disabled", save_selected: "yes", target: test1, state: present, comment: "Added by Ansible"}
        CONFIGURATIONS:
            - {name: "24Ghz", ssid: "test", channel_name: "24Ghz",  country: russia4, datapath_name: datapath, distance: indoor, guard_interval: long, hw_protection_mode: "rts-cts", installation: indoor, keepalive_frames: enabled, max_sta_count: "15", multicast_helper: "full", rx_chains: "0,1,2,3", tx_chains: "0,1,2,3", security_name: "security", target: test1, state: present, comment: "Added by Ansible"}
            - {name: "5Ghz",  ssid: "test", channel_name: "5Ghz",   country: russia4, datapath_name: datapath, distance: indoor, guard_interval: long, hw_protection_mode: "rts-cts", installation: indoor, keepalive_frames: enabled, max_sta_count: "15", multicast_helper: "full", rx_chains: "0,1,2,3", tx_chains: "0,1,2,3", security_name: "security", target: test1, state: present, comment: "Added by Ansible"}
        PROVISIONING:
            - {action: "create-dynamic-enabled", hw_supported_modes: "gn",    master_configuration_name: "24Ghz", name_format: "prefix-identity", name_prefix: "24GHz", target: test1, state: present, comment: "Added by Ansible"}
            - {action: "create-dynamic-enabled", hw_supported_modes: "an,ac", master_configuration_name: "5Ghz",  name_format: "prefix-identity", name_prefix: "5GHz",  target: test1, state: present, comment: "Added by Ansible"}
            - {action: "none",                   hw_supported_modes: "",      master_configuration_name: "24Ghz", name_format: "prefix-identity", name_prefix: "",      target: test1, state: present, comment: "Added by Ansible"}
```