Module: assemble  
Documentation : http://docs.ansible.com/ansible/assemble_module.html  
Installation: this is a core module. It ships with ansible itself  
it is idempotent  

Playbooks:
- **pb.yml**: Assembles all the junos configuration files in the directory fragments into a junos configuration file named junos.conf    

Usage:
```
# ls assemble/
fragments  pb.yml  readme.md
```   
```
# ls assemble/fragments/
bgp.txt  syslog.txt  system.txt

```
```
# ansible-playbook assemble/pb.yml 

PLAY [create configuration] **********************************************************************************************************************************************

TASK [Assembling configurations] *****************************************************************************************************************************************
changed: [localhost]

PLAY RECAP ***************************************************************************************************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0   

```
```
# ls assemble/
fragments  junos.conf  pb.yml  readme.md

```
```
# more assemble/junos.conf 
set routing-options autonomous-system 1191
set routing-options forwarding-table export bgp-ecmp
set protocols bgp group Ansibletraining type external
set protocols bgp group Ansibletraining family inet unicast
set protocols bgp group Ansibletraining local-as 104
set protocols bgp group Ansibletraining neighbor 192.168.0.1 peer-as 109
set protocols bgp group Ansibletraining neighbor 192.168.0.3 peer-as 110
set protocols bgp group Ansibletraining multipath multiple-as
set protocols bgp group Ansibletraining import bgp-in
set protocols bgp group Ansibletraining export bgp-out
set policy-options policy-statement bgp-ecmp then load-balance per-packet
set policy-options policy-statement bgp-in then accept
set policy-options policy-statement bgp-out then accept
set system syslog user * any emergency
set system syslog host 172.30.189.1 any notice
set system syslog host 172.30.189.1 authorization info
set system syslog host 172.30.189.1 interactive-commands info
set system syslog host 172.30.189.2 any notice
set system syslog host 172.30.189.2 authorization info
#set system syslog host 172.30.189.2 interactive-commands info
set system syslog file messages any notice
set system syslog file messages authorization info
set system services ftp
set system services ssh
set system services telnet
set system services netconf ssh
set system domain-name ansible-training.jnpr.net
set system time-zone Europe/Amsterdam
set system name-server 8.8.8.8
```
