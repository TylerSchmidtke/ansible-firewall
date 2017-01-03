# ANSIBLE-FIREWALL
## iptables management

This role manages iptables rules on Debian/Ubuntu systems. It is designed to be simple to use and does not fully implement all features of iptables. If you need more advanced iptables configuration, I recommend writing the rules file manually or make a pull request to add functionality to this role.

The role installs rules.v4 and iptables_load files which are used to load the rules.

This role is unique from other Ansible firewall roles in that it can operate not only on hosts, but also groups defined in your Ansible inventory. This means that your rules will update if the hosts in your inventory change. It also means that declaration of hosts is far simpler, as you can just use the group name, rather than defining each host.

**LIMITATIONS**  
TCP and UDP ports must be defined in separate dictionaries. UDP is defined using the ```protocol``` key. The default protocol is TCP.
Groups defined in ```iptables_allowed_group_rules``` include the hostnames/IPs as they are defined in the Ansible inventory file, not based on facts from the host


###Default variables:
```
iptables_confdir: /etc/iptables
iptables_rules_path: "{{ iptables_confdir }}/rules.v4"
iptables_load_path: /etc/network/if-up.d/iptables_load

iptables_logging: true
iptables_flush_all: true # Prepend rules with iptables
iptables_input_deny_all: true #Default INPUT policy is DROP
iptables_forward_deny_all: true #Default FORWARD policy is DROP
iptables_output_deny_all: false #Default INPUT policy is DROP

iptables_allowed_tcp_ports:
  - 22
iptables_allowed_udp_ports: []
iptables_allowed_host_rules: []
iptables_allowed_group_rules: []
iptables_allow_icmp: true
```   
### Examples plays:
```
# Allow incoming TCP 22, 80, and 443
- hosts: server
  roles:
    - role: ansible-firewall
      iptables_allowed_tcp_ports:
        - 22
        - 80
        - 443

# Allow incoming TCP 22, 80, and 443 and UDP 53 for a specific network
- hosts: server
  roles:
    - role: ansible-firewall
      iptables_allowed_host_rules:
        - host: 192.168.1.0/24
          ports:
            - 22
            - 80
            - 443
        - host: 192.168.1.0/24
          ports:
            - 53
          protocol: udp

# Allow incoming 9200 from the "logstash" group defined in the inventory,
# 9200 and 9300 from the "elasticsearch" group defined in the inventory.
# Hostnames or IPs as defined in the inventory file are used
- hosts: elasticsearch
  roles:
    - role: ansible-firewall
      iptables_allowed_group_rules:
        - group: logstash
          ports:
            - 9200
        - group: elasticsearch
          ports:
            - 9200
            - 9300
```  

### TODO
1. Add support for IPv6
2. Add support for Redhat

### License
MIT

### Suggestions/Issues?
File an issue, or open a PR.
