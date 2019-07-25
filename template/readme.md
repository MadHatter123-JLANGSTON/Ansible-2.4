Module: template   
it creates a document based on a Jinja2 template.  
Documentation : http://docs.ansible.com/ansible/template_module.html  
Installation: this is a core module. It ships with ansible itself  

Playbooks:  

- [**pb_render_initial_configuration.yml**](pb_render_initial_configuration.yml): creates junos configuration for new devices based on the template [**initial_configuration.j2**](initial_configuration.j2). The junos configuration files created are stored in the directory [**render**](render). Nothing is loaded on Junos devices.  
- [**pb_render_common_settings.yml**](pb_render_common_settings.yml): creates junos configuration for common settings (syslog, ntp, snmp...) based on the template [**common_settings.j2**](common_settings.j2). The junos configuration files created are stored in the directory [**render**](render). Nothing is loaded on Junos devices.  
- [**pb_render_bgp.yml**](pb_render_bgp.yml): creates Junos BGP configuration based on the template [**bgp.j2**](bgp.j2). The junos configuration files created are stored in the directory [**render**](render). Nothing is loaded on Junos devices.  
- [**pb_load_cfg_from_template.yml**](pb_load_cfg_from_template.yml): uses the template [**dns-servers.j2**](dns-servers.j2) and the variables [**dns-servers.yml**](dns-servers.yml) to create a Junos configuration file to add DNS servers, and applies the document to Junos devices. This playbook adds DNS servers to the existing configuration on devices. 
- [**pb_load_cfg_from_template_replace.yml**](pb_load_cfg_from_template_replace.yml): uses the template [**dns-servers_replace.j2**](dns-servers_replace.j2) and the variables [**dns-servers.yml**](dns-servers.yml) to create a Junos configuration file to add DNS servers, and applies the document to Junos devices. This playbook replaces the DNS configuration on devices. The diff is saved in the directory [**diff**](diff)

Usage:   
# pb_render_common_settings.yml
```
# ls template/
bgp.j2              dns-servers_replace.j2    pb_load_cfg_from_template_replace.yml  pb_render_common_settings.yml
common_settings.j2  dns-servers.yml           pb_load_cfg_from_template.yml          pb_render_initial_configuration.yml
dns-servers.j2      initial_configuration.j2  pb_render_bgp.yml                      readme.md

```
```
# ansible-playbook template/pb_render_common_settings.yml 

PLAY [create a directory] ************************************************************************************************************************************************

TASK [create the directory render] ***************************************************************************************************************************************
changed: [localhost]

PLAY [create common settings configuration] ******************************************************************************************************************************

TASK [Render initial configuration for junos devices] ********************************************************************************************************************
changed: [ex4300-18]
changed: [ex4300-9]
changed: [ex4300-17]

PLAY RECAP ***************************************************************************************************************************************************************
ex4300-17                  : ok=1    changed=1    unreachable=0    failed=0   
ex4300-18                  : ok=1    changed=1    unreachable=0    failed=0   
ex4300-9                   : ok=1    changed=1    unreachable=0    failed=0   
localhost                  : ok=1    changed=1    unreachable=0    failed=0   

```
```
# ls template/render/
ex4300-17.common_settings.conf  ex4300-18.common_settings.conf  ex4300-9.common_settings.conf
```
```
# more template/render/ex4300-17.common_settings.conf 
system {
    time-zone America/Los_Angeles;
    name-server {
        8.8.8.8;
        8.8.8.4;
    }
    login {
        message "This is the banner ";
    }
    ntp {
        server 172.17.28.5;
    }
    syslog {
        user * {
            any emergency;
        }
        file messages {
            any notice;
            authorization info;
        }
        file interactive-commands {
            interactive-commands any;
        }
    }
}
snmp {
    location "DC4";
    contact "xxxx@juniper.net";
}
protocols {
    lldp {
        interface all;
    }
}

```
# pb_render_initial_configuration.yml

```
# ls template/render/
ex4300-17.common_settings.conf  ex4300-18.common_settings.conf  ex4300-9.common_settings.conf
```
```
# ansible-playbook template/pb_render_initial_configuration.yml --check --diff --limit ex4300-17

PLAY [create a directory] ************************************************************************************************************************************************
skipping: no hosts matched

PLAY [create initial junos configuration] ********************************************************************************************************************************

TASK [Render initial configuration for junos devices] ********************************************************************************************************************
--- before
+++ after: /tmp/tmpzyD3eT/initial_configuration.j2
@@ -0,0 +1,27 @@
+system {
+    host-name ex4300-17;
+    root-authentication {
+        encrypted-password $1$/NHg28eO$pqaVlLlPQ2thlQQ0ZB.Vx/; 
+    }
+    services {
+        ssh;
+        netconf {
+            ssh;
+        }
+    }
+}    
+interfaces {
+   em0 {
+        description "management interface";
+        unit 0 {
+            family inet {
+                address 172.30.179.73/24;
+            }
+        }
+    }
+}
+routing-options {
+    static {
+        route 0.0.0.0/0 next-hop 172.30.179.1;
+    }
+}

changed: [ex4300-17]

PLAY RECAP ***************************************************************************************************************************************************************
ex4300-17                  : ok=1    changed=1    unreachable=0    failed=0   
```
```
# ls template/render/
ex4300-17.common_settings.conf  ex4300-18.common_settings.conf  ex4300-9.common_settings.conf
```
```
# ansible-playbook template/pb_render_initial_configuration.yml

PLAY [create a directory] ************************************************************************************************************************************************

TASK [create the directory render] ***************************************************************************************************************************************
ok: [localhost]

PLAY [create initial junos configuration] ********************************************************************************************************************************

TASK [Render initial configuration for junos devices] ********************************************************************************************************************
changed: [ex4300-17]
changed: [ex4300-9]
changed: [ex4300-18]

PLAY RECAP ***************************************************************************************************************************************************************
ex4300-17                  : ok=1    changed=1    unreachable=0    failed=0   
ex4300-18                  : ok=1    changed=1    unreachable=0    failed=0   
ex4300-9                   : ok=1    changed=1    unreachable=0    failed=0   
localhost                  : ok=1    changed=0    unreachable=0    failed=0   

```
```
# ls template/render/*initial_configuration.conf
template/render/ex4300-17.initial_configuration.conf  template/render/ex4300-18.initial_configuration.conf  template/render/ex4300-9.initial_configuration.conf
```
```
# more template/render/ex4300-18.initial_configuration.conf 
system {
    host-name ex4300-18;
    root-authentication {
        encrypted-password $1$/NHg28eO$pqaVlLlPQ2thlQQ0ZB.Vx/; 
    }
    services {
        ssh;
        netconf {
            ssh;
        }
    }
}    
interfaces {
   em0 {
        description "management interface";
        unit 0 {
            family inet {
                address 172.30.179.74/24;
            }
        }
    }
}
routing-options {
    static {
        route 0.0.0.0/0 next-hop 172.30.179.1;
    }
}

```
# pb_render_bgp.yml

```
# ls template/render/
ex4300-17.common_settings.conf        ex4300-18.common_settings.conf        ex4300-9.common_settings.conf
ex4300-17.initial_configuration.conf  ex4300-18.initial_configuration.conf  ex4300-9.initial_configuration.conf
```
```
# ansible-playbook template/pb_render_bgp.yml

PLAY [create a directory] ************************************************************************************************************************************************

TASK [create the directory render] ***************************************************************************************************************************************
ok: [localhost]

PLAY [create BGP junos configuration] ************************************************************************************************************************************

TASK [Render BGP configuration for junos devices] ************************************************************************************************************************
changed: [ex4300-9]
changed: [ex4300-18]
changed: [ex4300-17]

PLAY RECAP ***************************************************************************************************************************************************************
ex4300-17                  : ok=1    changed=1    unreachable=0    failed=0   
ex4300-18                  : ok=1    changed=1    unreachable=0    failed=0   
ex4300-9                   : ok=1    changed=1    unreachable=0    failed=0   
localhost                  : ok=1    changed=0    unreachable=0    failed=0   

```
```
# ls template/render/*bgp.conf
template/render/ex4300-17.bgp.conf  template/render/ex4300-18.bgp.conf  template/render/ex4300-9.bgp.conf
```
```
# more template/render/ex4300-18.bgp.conf 
interfaces {
    ge-0/0/0 {
        unit 0 {
            description "ex4300-9";
            family inet {
                address 192.168.0.0/31;
            }
        }
    }
    ge-0/0/1 {
        unit 0 {
            description "ex4300-17";
            family inet {
                address 192.168.0.3/31;
            }
        }
    }
}
protocols {
    bgp {
        group underlay {
            import bgp-in;
            export bgp-out;
            type external;
            local-as 104;
            multipath multiple-as;
            neighbor 192.168.0.1 {
                peer-as 109;
            }
            neighbor 192.168.0.2 {
                peer-as 110;
            }
        }
    }
    lldp {
        interface all;
    }
}

routing-options {
    router-id 192.179.0.74;
    forwarding-table {
        export bgp-ecmp;
    }
}

policy-options {
    policy-statement bgp-ecmp {
        then {
            load-balance per-packet;
        }
    }
    policy-statement bgp-in {
        then accept;
    }
    policy-statement bgp-out {
        then accept;
    }
}

```
# pb_load_cfg_from_template.yml

```
# ansible-playbook template/pb_load_cfg_from_template.yml

PLAY [create a directory render] *****************************************************************************************************************************************

TASK [create and push the directory render] ******************************************************************************************************************************
changed: [localhost]

PLAY [Load Configs From Jinja2 Templates] ********************************************************************************************************************************

TASK [Retrieve information from devices running Junos OS] ****************************************************************************************************************
ok: [ex4300-17]
ok: [ex4300-18]
ok: [ex4300-9]

TASK [Create Junos configurations from Jinja2] ***************************************************************************************************************************
changed: [ex4300-18]
changed: [ex4300-17]
changed: [ex4300-9]

TASK [Install rendered configuration] ************************************************************************************************************************************
changed: [ex4300-9]
changed: [ex4300-18]
changed: [ex4300-17]

PLAY RECAP ***************************************************************************************************************************************************************
ex4300-17                  : ok=3    changed=2    unreachable=0    failed=0   
ex4300-18                  : ok=3    changed=2    unreachable=0    failed=0   
ex4300-9                   : ok=3    changed=2    unreachable=0    failed=0   
localhost                  : ok=1    changed=1    unreachable=0    failed=0   
```
```
# ls template/render/
ex4300-17.conf  ex4300-18.conf  ex4300-9.conf
```
```
# more template/render/ex4300-17.conf 
system {
    name-server {
                8.8.8.8;
                8.8.4.4;
            }
}
```
```
# more template/ex4300-17.log 

[edit system name-server]
    172.30.179.3 { ... }
+   8.8.8.8;
+   8.8.4.4;
```
```
# ssh pytraining@172.30.179.73
Password:
--- JUNOS 15.1R5.5 built 2016-11-25 16:55:57 UTC
pytraining@ex4300-17> show system commit 
0   2018-01-23 20:49:19 CET by pytraining via netconf
    commit from ansible playbook pb_load_cfg_from_template.yml
```                               
```
pytraining@ex4300-17> show configuration | compare rollback 1 
[edit system name-server]
    172.30.179.3 { ... }
+   8.8.8.8;
+   8.8.4.4;
```
# pb_load_cfg_from_template_replace.yml

```
# ansible-playbook template/pb_load_cfg_from_template_replace.yml --check --diff --limit ex4300-9

PLAY [create a directory render] *****************************************************************************************************************************************
skipping: no hosts matched

PLAY [Load Configs From Jinja2 Templates] ********************************************************************************************************************************

TASK [Retrieve information from devices running Junos OS] ****************************************************************************************************************
ok: [ex4300-9]

TASK [Create Junos configurations from Jinja2] ***************************************************************************************************************************
--- before: /home/ksator/ansible-training-for-junos-automation/template/render/ex4300-9.conf
+++ after: /tmp/tmpTfSxe6/dns-servers_replace.j2
@@ -1,4 +1,5 @@
 system {
+    replace:
     name-server {
                 8.8.8.8;
                 8.8.4.4;

changed: [ex4300-9]

TASK [Install rendered configuration] ************************************************************************************************************************************
ok: [ex4300-9]

PLAY RECAP ***************************************************************************************************************************************************************
ex4300-9                   : ok=3    changed=1    unreachable=0    failed=0   
```
```
# ansible-playbook template/pb_load_cfg_from_template_replace.yml

PLAY [create a directory render] *****************************************************************************************************************************************

TASK [create directories] ************************************************************************************************************************************************
ok: [localhost] => (item=render)
changed: [localhost] => (item=diff)

PLAY [Load Configs From Jinja2 Templates] ********************************************************************************************************************************

TASK [Retrieve information from devices running Junos OS] ****************************************************************************************************************
ok: [ex4300-18]
ok: [ex4300-17]
ok: [ex4300-9]

TASK [Create Junos configurations from Jinja2] ***************************************************************************************************************************
changed: [ex4300-17]
changed: [ex4300-18]
changed: [ex4300-9]

TASK [Install rendered configuration] ************************************************************************************************************************************
changed: [ex4300-17]
changed: [ex4300-9]
changed: [ex4300-18]

PLAY RECAP ***************************************************************************************************************************************************************
ex4300-17                  : ok=3    changed=2    unreachable=0    failed=0   
ex4300-18                  : ok=3    changed=2    unreachable=0    failed=0   
ex4300-9                   : ok=3    changed=2    unreachable=0    failed=0   
localhost                  : ok=1    changed=1    unreachable=0    failed=0   
```
```
# ls template/diff/
ex4300-17.log  ex4300-18.log  ex4300-9.log
```
```
# more template/diff/ex4300-9.log 

[edit system name-server]
-   172.30.179.2;
-   172.30.179.3;
```
```
# more template/render/ex4300-9.conf 
system {
    replace:
    name-server {
                8.8.8.8;
                8.8.4.4;
            }
}
```
```
# ssh pytraining@172.30.179.73
Password:
--- JUNOS 15.1R5.5 built 2016-11-25 16:55:57 UTC
{master:0}
pytraining@ex4300-17> show system commit 
0   2018-01-23 21:03:40 CET by pytraining via netconf
```
```
pytraining@ex4300-17> show configuration | compare rollback 1 
[edit system name-server]
-   172.30.179.2;
-   172.30.179.3;
```
```
pytraining@ex4300-17> show configuration system name-server 
8.8.8.8;
8.8.4.4;
```
