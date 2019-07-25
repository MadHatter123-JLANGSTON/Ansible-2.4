Module: junos_command  
Executes commands (cli or rpc) on Junos devices and return the results to the Ansible playbook.  
This module includes an optional argument that cause the module to wait for a specific condition.  
Documentation: http://docs.ansible.com/ansible/junos_command_module.html  
source code: https://github.com/ansible/ansible/tree/devel/lib/ansible/modules/network/junos  
Installation: this is a core module. It ships with ansible itself      

# Important Notes: 

This ansible module provide a diff output from devices based on Ansible versions.  
So the parsing (using the **wait_for** optionnal argument from this module) depends on Ansible versions.    

### Ansible 2.2.3
- the structured response is returned in the 'stdout' key. 
- the structured response does not include the 'rpc-reply' key.  
- If you register a variable named response
  - then the structured response for the first command in commands is response['stdout'][0] if you are not using with_items
  - or response['results'][item_number]['stdout'][0] if you are using with_items.
- The implicit result variable used in the wait_for conditions maps to response['results'][current_item_number]['stdout'].

```
# ansible --version
ansible 2.2.3.0
```
```
# more junos_command/pb.check.bgp.yml 
---
 - name: check bgp states (with items)
   hosts: AMS-EX4300
   connection: local
   gather_facts: no

   tasks:
   - name: check if bgp neighbors are established
     junos_command:
      provider: "{{  credentials }}"
      commands:
       - show bgp neighbor "{{ item.peer_ip }}"
      waitfor:
#      - "result[0].bgp-information.bgp-peer.peer-state eq Established"
      - "result[0]['bgp-information']['bgp-peer']['peer-state'] eq Established"
     with_items:
      - "{{ neighbors }}"
     register: junos

   - name: "Debug with items. first item"
     debug:
        var: junos['results'][0]['stdout'][0]['bgp-information']['bgp-peer']['peer-state']

   - name: "Debug with items. second item"
     debug:
        var: junos['results'][1]['stdout'][0]['bgp-information']['bgp-peer']['peer-state']
      
 - name: check bgp states (without item)
   hosts: ex4300-9
   connection: local
   gather_facts: no

   tasks:


   - name: check if bgp neighbors are established
     junos_command:
      provider: "{{  credentials }}"
      commands:
       - show bgp neighbor 192.168.0.0
      waitfor:
      - "result[0]['bgp-information']['bgp-peer']['peer-state'] eq Established"
     register: junos

   - name: "Debug without item"
     debug:
        var: junos['stdout'][0]['bgp-information']['bgp-peer']['peer-state']

```
```
# ansible-playbook junos_command/pb.check.bgp.yml 

PLAY [check bgp states (with items)] *******************************************

TASK [check if bgp neighbors are established] **********************************
ok: [ex4300-18] => (item={u'peer_loopback': u'192.179.0.95', u'local_ip': u'192.168.0.0', u'peer_ip': u'192.168.0.1', u'interface': u'ge-0/0/0', u'asn': 109, u'name': u'ex4300-9'})
ok: [ex4300-9] => (item={u'peer_loopback': u'192.179.0.73', u'local_ip': u'192.168.0.5', u'peer_ip': u'192.168.0.4', u'interface': u'ge-0/0/0', u'asn': 110, u'name': u'ex4300-17'})
ok: [ex4300-17] => (item={u'peer_loopback': u'192.179.0.95', u'local_ip': u'192.168.0.4', u'peer_ip': u'192.168.0.5', u'interface': u'ge-0/0/0', u'asn': 109, u'name': u'ex4300-9'})
ok: [ex4300-18] => (item={u'peer_loopback': u'192.179.0.73', u'local_ip': u'192.168.0.3', u'peer_ip': u'192.168.0.2', u'interface': u'ge-0/0/1', u'asn': 110, u'name': u'ex4300-17'})
ok: [ex4300-9] => (item={u'peer_loopback': u'192.179.0.74', u'local_ip': u'192.168.0.1', u'peer_ip': u'192.168.0.0', u'interface': u'ge-0/0/1', u'asn': 104, u'name': u'ex4300-18'})
ok: [ex4300-17] => (item={u'peer_loopback': u'192.179.0.74', u'local_ip': u'192.168.0.2', u'peer_ip': u'192.168.0.3', u'interface': u'ge-0/0/1', u'asn': 104, u'name': u'ex4300-18'})

TASK [Debug with items. first item] ********************************************
ok: [ex4300-9] => {
    "junos['results'][0]['stdout'][0]['bgp-information']['bgp-peer']['peer-state']": "Established"
}
ok: [ex4300-17] => {
    "junos['results'][0]['stdout'][0]['bgp-information']['bgp-peer']['peer-state']": "Established"
}
ok: [ex4300-18] => {
    "junos['results'][0]['stdout'][0]['bgp-information']['bgp-peer']['peer-state']": "Established"
}

TASK [Debug with items. second item] *******************************************
ok: [ex4300-18] => {
    "junos['results'][1]['stdout'][0]['bgp-information']['bgp-peer']['peer-state']": "Established"
}
ok: [ex4300-17] => {
    "junos['results'][1]['stdout'][0]['bgp-information']['bgp-peer']['peer-state']": "Established"
}
ok: [ex4300-9] => {
    "junos['results'][1]['stdout'][0]['bgp-information']['bgp-peer']['peer-state']": "Established"
}

PLAY [check bgp states (without item)] *****************************************

TASK [check if bgp neighbors are established] **********************************
ok: [ex4300-9]

TASK [Debug without item] ******************************************************
ok: [ex4300-9] => {
    "junos['stdout'][0]['bgp-information']['bgp-peer']['peer-state']": "Established"
}

PLAY RECAP *********************************************************************
ex4300-17                  : ok=3    changed=0    unreachable=0    failed=0   
ex4300-18                  : ok=3    changed=0    unreachable=0    failed=0   
ex4300-9                   : ok=5    changed=0    unreachable=0    failed=0   

```

### Ansible 2.3.2

```
$ ansible --version
ansible 2.3.2.0
```
##### with xml

- The structured response DOES include the 'rpc-reply' key.  

```
$ more junos_command/pb.check.bgp.xml.yml 
---
 - name: check bgp states
   hosts: AMS-EX4200
   connection: local
   gather_facts: no
   tasks:

   - name: check if bgp neighbors are established
     junos_command:
      provider: "{{  credentials }}"
      display: 'xml'
      commands:
       - show bgp neighbor "{{ item.peer_ip }}"
      waitfor:
       - "result[0].rpc-reply.bgp-information.bgp-peer.peer-state eq 'Established'"
     with_items:
      - "{{ neighbors }}"

   - name: check if bgp neighbors are established using another sysntax
     junos_command:
      provider: "{{  credentials }}"
      display: 'xml'
      commands:
       - show bgp neighbor "{{ item.peer_ip }}"
      waitfor:
        - "result[0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state'] eq 'Established'"
     with_items:
      - "{{ neighbors }}"
```
```
$ ansible-playbook junos_command/pb.check.bgp.xml.yml 

PLAY [check bgp states] ***********************************************************************************************************************************************************

TASK [check if bgp neighbors are established] *************************************************************************************************************************************
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.0', u'peer_ip': u'192.168.10.1', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-7] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.5', u'peer_ip': u'192.168.10.4', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})
ok: [ex4200-8] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.4', u'peer_ip': u'192.168.10.5', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.3', u'peer_ip': u'192.168.10.2', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})
ok: [ex4200-7] => (item={u'peer_loopback': u'192.179.0.112', u'local_ip': u'192.168.10.1', u'peer_ip': u'192.168.10.0', u'interface': u'ge-0/0/0', u'asn': 204, u'name': u'ex4200-12'})
ok: [ex4200-8] => (item={u'peer_loopback': u'192.179.0.112', u'local_ip': u'192.168.10.2', u'peer_ip': u'192.168.10.3', u'interface': u'ge-0/0/1', u'asn': 204, u'name': u'ex4200-12'})

TASK [check if bgp neighbors are established using another sysntax] ***************************************************************************************************************
ok: [ex4200-7] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.5', u'peer_ip': u'192.168.10.4', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.0', u'peer_ip': u'192.168.10.1', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-8] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.4', u'peer_ip': u'192.168.10.5', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.3', u'peer_ip': u'192.168.10.2', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})
ok: [ex4200-7] => (item={u'peer_loopback': u'192.179.0.112', u'local_ip': u'192.168.10.1', u'peer_ip': u'192.168.10.0', u'interface': u'ge-0/0/0', u'asn': 204, u'name': u'ex4200-12'})
ok: [ex4200-8] => (item={u'peer_loopback': u'192.179.0.112', u'local_ip': u'192.168.10.2', u'peer_ip': u'192.168.10.3', u'interface': u'ge-0/0/1', u'asn': 204, u'name': u'ex4200-12'})

PLAY RECAP ************************************************************************************************************************************************************************
ex4200-12                  : ok=2    changed=0    unreachable=0    failed=0   
ex4200-7                   : ok=2    changed=0    unreachable=0    failed=0   
ex4200-8                   : ok=2    changed=0    unreachable=0    failed=0   


```


##### with json   

Parsing for json output from ex4200-24t running Junos 15.1R2.9:  

```
$ more junos_command/pb.check.bgp.yml
---
 - name: check bgp states
   hosts: ex4200-12
   connection: local
   gather_facts: no
   # ansible 2.3.2 parsing for json output from ex4200-24t running Junos 15.1R2.9
   tasks:

   - name: check if bgp neighbors are established
     junos_command:
      provider: "{{  credentials }}"
      display: json
      commands:
       - show bgp neighbor "{{ item.peer_ip }}"
      waitfor:
       - "result[0].bgp-information[0].bgp-peer[0].peer-state[0].data eq Established"
     register: bgpdetails
     with_items:
      - "{{ neighbors }}"

   - name: check if bgp neighbors are established using another sysntax
     junos_command:
      provider: "{{  credentials }}"
      display: json
      commands:
       - show bgp neighbor "{{ item.peer_ip }}"
      waitfor:
        - "result[0]['bgp-information'][0]['bgp-peer'][0]['peer-state'][0]['data'] eq Established"
     register: bgp
     with_items:
      - "{{ neighbors }}"

   - name: print bgp state for item number 0
     debug: 
       msg="bgp session state for item 0 is {{ bgp.results[0].stdout[0]['bgp-information'][0]['bgp-peer'][0]['peer-state'][0]['data'] }}"

   - name: print bgp state for item number 1
     debug:
       msg="bgp session state for item 1 is {{bgp.results[1].stdout[0]['bgp-information'][0]['bgp-peer'][0]['peer-state'][0]['data']}}"

   - name: "print bgp states for all items"
     debug:
        msg="bgp state is {{ item.stdout[0]['bgp-information'][0]['bgp-peer'][0]['peer-state'][0]['data'] }}"
     with_items: "{{ bgp['results'] }}"
     no_log: True

   - name: assert bgp state is established for item number 0
     assert:
       that:
       - "'Established' in bgp.results[0].stdout[0]['bgp-information'][0]['bgp-peer'][0]['peer-state'][0]['data']"
       msg: "bgp state on device {{ inventory_hostname }} is not established"

   - name: assert bgp state is established for item number 1
     assert:
       that:
       - "bgp.results[1].stdout[0]['bgp-information'][0]['bgp-peer'][0]['peer-state'][0]['data'] == 'Established'"
       msg: "bgp state on device {{ inventory_hostname }} is not established"

   - name: assert bgp state is established for all items
     assert:
       that:
       - "item.stdout[0]['bgp-information'][0]['bgp-peer'][0]['peer-state'][0]['data'] == 'Established'"
       msg: "bgp state on device {{ inventory_hostname }} is not established"
     with_items: "{{ bgp['results'] }}"
     no_log: True

```
```
$ ansible-playbook junos_command/pb.check.bgp.yml

PLAY [check bgp states] ***********************************************************************************************************************************************************

TASK [check if bgp neighbors are established] *************************************************************************************************************************************
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.0', u'peer_ip': u'192.168.10.1', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.3', u'peer_ip': u'192.168.10.2', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})

TASK [check if bgp neighbors are established using another sysntax] ***************************************************************************************************************
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.0', u'peer_ip': u'192.168.10.1', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.3', u'peer_ip': u'192.168.10.2', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})

TASK [print bgp state for item number 0] ******************************************************************************************************************************************
ok: [ex4200-12] => {
    "msg": "bgp session state for item 0 is Established"
}

TASK [print bgp state for item number 1] ******************************************************************************************************************************************
ok: [ex4200-12] => {
    "msg": "bgp session state for item 1 is Established"
}

TASK [print bgp states for all items] *********************************************************************************************************************************************
ok: [ex4200-12] => (item=(censored due to no_log)) => {"censored": "the output has been hidden due to the fact that 'no_log: true' was specified for this result"}
ok: [ex4200-12] => (item=(censored due to no_log)) => {"censored": "the output has been hidden due to the fact that 'no_log: true' was specified for this result"}

TASK [assert bgp state is established for item number 0] **************************************************************************************************************************
ok: [ex4200-12] => {
    "changed": false, 
    "msg": "All assertions passed"
}

TASK [assert bgp state is established for item number 1] **************************************************************************************************************************
ok: [ex4200-12] => {
    "changed": false, 
    "msg": "All assertions passed"
}

TASK [assert bgp state is established for all items] ******************************************************************************************************************************
ok: [ex4200-12] => (item=(censored due to no_log)) => {"censored": "the output has been hidden due to the fact that 'no_log: true' was specified for this result"}
ok: [ex4200-12] => (item=(censored due to no_log)) => {"censored": "the output has been hidden due to the fact that 'no_log: true' was specified for this result"}

PLAY RECAP ************************************************************************************************************************************************************************
ex4200-12                  : ok=8    changed=0    unreachable=0    failed=0   

```

##### Ansible 2.4

```
# ansible --version
ansible 2.4.0.0
```
- the structured response DOES include the 'rpc-reply' key.
- the structured response is returned in the 'output' key. 
```
# more junos_command/pb.check.bgp.xml.yml 
---
 - name: check bgp states with items
   hosts: AMS-EX4200
   connection: local
   gather_facts: no
   tasks:

   - name: check if bgp neighbors are established
     junos_command:
      provider: "{{  credentials }}"
      display: 'xml'
      commands:
       - show bgp neighbor "{{ item.peer_ip }}"
      waitfor:
       - "result[0].rpc-reply.bgp-information.bgp-peer.peer-state eq 'Established'"
     with_items:
      - "{{ neighbors }}"
     register: response

   - name: check if bgp neighbors are established using another sysntax
     junos_command:
      provider: "{{  credentials }}"
      display: 'xml'
      commands:
       - show bgp neighbor "{{ item.peer_ip }}"
      waitfor:
        - "result[0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state'] eq 'Established'"
     with_items:
      - "{{ neighbors }}"

    
   - name: "Debug with items. 1st item"
     debug:
        var: response['results'][0]['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']
     
   - name: "Debug with items. last item"
     debug:
        var: response['results'][-1]['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']

  
 - name: check bgp states without item
   hosts: ex4200-12
   connection: local
   gather_facts: no
   tasks:

   - name: check if bgp neighbors are established
     junos_command:
      provider: "{{  credentials }}"
      display: 'xml'
      commands:
       - show bgp neighbor 192.168.10.1
      waitfor:
       - "result[0].rpc-reply.bgp-information.bgp-peer.peer-state eq 'Established'"
     register: response


   - name: "Debug without item"
     debug:
        var: response['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']

```
```
# ansible-playbook junos_command/pb.check.bgp.xml.yml 

PLAY [check bgp states with items] ************************************************************************************************************************************************

TASK [check if bgp neighbors are established] *************************************************************************************************************************************
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.0', u'peer_ip': u'192.168.10.1', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-7] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.5', u'peer_ip': u'192.168.10.4', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})
ok: [ex4200-8] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.4', u'peer_ip': u'192.168.10.5', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.3', u'peer_ip': u'192.168.10.2', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})
ok: [ex4200-7] => (item={u'peer_loopback': u'192.179.0.112', u'local_ip': u'192.168.10.1', u'peer_ip': u'192.168.10.0', u'interface': u'ge-0/0/0', u'asn': 204, u'name': u'ex4200-12'})
ok: [ex4200-8] => (item={u'peer_loopback': u'192.179.0.112', u'local_ip': u'192.168.10.2', u'peer_ip': u'192.168.10.3', u'interface': u'ge-0/0/1', u'asn': 204, u'name': u'ex4200-12'})

TASK [check if bgp neighbors are established using another sysntax] ***************************************************************************************************************
ok: [ex4200-8] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.4', u'peer_ip': u'192.168.10.5', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.107', u'local_ip': u'192.168.10.0', u'peer_ip': u'192.168.10.1', u'interface': u'ge-0/0/0', u'asn': 209, u'name': u'ex4200-7'})
ok: [ex4200-7] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.5', u'peer_ip': u'192.168.10.4', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})
ok: [ex4200-8] => (item={u'peer_loopback': u'192.179.0.112', u'local_ip': u'192.168.10.2', u'peer_ip': u'192.168.10.3', u'interface': u'ge-0/0/1', u'asn': 204, u'name': u'ex4200-12'})
ok: [ex4200-12] => (item={u'peer_loopback': u'192.179.0.108', u'local_ip': u'192.168.10.3', u'peer_ip': u'192.168.10.2', u'interface': u'ge-0/0/1', u'asn': 210, u'name': u'ex4200-8'})
ok: [ex4200-7] => (item={u'peer_loopback': u'192.179.0.112', u'local_ip': u'192.168.10.1', u'peer_ip': u'192.168.10.0', u'interface': u'ge-0/0/0', u'asn': 204, u'name': u'ex4200-12'})

TASK [Debug with items. 1st item] *************************************************************************************************************************************************
ok: [ex4200-7] => {
    "response['results'][0]['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']": "Established"
}
ok: [ex4200-8] => {
    "response['results'][0]['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']": "Established"
}
ok: [ex4200-12] => {
    "response['results'][0]['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']": "Established"
}

TASK [Debug with items. last item] ************************************************************************************************************************************************
ok: [ex4200-7] => {
    "response['results'][-1]['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']": "Established"
}
ok: [ex4200-12] => {
    "response['results'][-1]['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']": "Established"
}
ok: [ex4200-8] => {
    "response['results'][-1]['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']": "Established"
}

PLAY [check bgp states without item] **********************************************************************************************************************************************

TASK [check if bgp neighbors are established] *************************************************************************************************************************************
ok: [ex4200-12]

TASK [Debug without item] *********************************************************************************************************************************************************
ok: [ex4200-12] => {
    "response['output'][0]['rpc-reply']['bgp-information']['bgp-peer']['peer-state']": "Established"
}

PLAY RECAP ************************************************************************************************************************************************************************
ex4200-12                  : ok=6    changed=0    unreachable=0    failed=0   
ex4200-7                   : ok=4    changed=0    unreachable=0    failed=0   
ex4200-8                   : ok=4    changed=0    unreachable=0    failed=0   
```


