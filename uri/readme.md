Module: uri   
Documenatation: http://docs.ansible.com/ansible/uri_module.html  
Installation: this is a core module. It ships with ansible itself.   
Dependency: The dependency on httplib2 was removed in Ansible 2.1  
Requirement on Junos: Enable rest api on Junos devices (default port is 3000)   
```
set system services rest http
```

Playbook: 
- **pb_rest_call.yml**: it check if the rest api default port (3000) is enabled on the QFX devices. it makes a rest call to the junos devices. It saves locally REST call output.  it uses the debug module to print some of the collected data.   
- **pb_rest_calls.yml**: it check if the rest api default port (3000) is enabled on the QFX devices. it makes several rest calls to the junos devices. It saves locally REST calls output.  

Usage:
# pb_rest_call.yml

```
# ls uri/
pb_rest_calls.yml  pb_rest_call.yml  readme.md  somelog.json
```
```
# ansible-playbook uri/pb_rest_call.yml 

PLAY [make rest call to Junos devices, save and parse output] *********************************************************************************************************************

TASK [check if some ports are reachable on Junos devices] *************************************************************************************************************************
ok: [qfx5100-44] => (item=22)
ok: [qfx5100-44] => (item=830)
ok: [qfx5100-44] => (item=3000)

TASK [create device directories] **************************************************************************************************************************************************
changed: [qfx5100-44 -> localhost]

TASK [make rest call to QFX devices] **********************************************************************************************************************************************
changed: [qfx5100-44 -> localhost]

TASK [Print some QFX details] *****************************************************************************************************************************************************
ok: [qfx5100-44] => {
    "msg": "device hostname qfx5100-44 is running 17.3R1.10"
}

PLAY RECAP ************************************************************************************************************************************************************************
qfx5100-44                 : ok=4    changed=2    unreachable=0    failed=0   
```
```
# ls uri/
pb_rest_calls.yml  pb_rest_call.yml  qfx5100-44  readme.md  somelog.json
```
```
# ls uri/qfx5100-44/
rpc_output.json
```
```
# more uri/qfx5100-44/rpc_output.json 
{
    "multi-routing-engine-results" : [
    {
        "multi-routing-engine-item" : [
        {
            "re-name" : [
            {
                "data" : "fpc0"
            }
            ], 
            "software-information" : [
            {
                "host-name" : [
                {
                    "data" : "qfx5100-44"
                }
                ], 
                "product-model" : [
                {
                    "data" : "qfx5100-48t-6q"
                }
                ], 
                "product-name" : [
                {
                    "data" : "qfx5100-48t-6q"
                }
                ], 
                "junos-version" : [
                {
                    "data" : "17.3R1.10"
                }
                ], 
                "package-information" : [
                {
                    "name" : [
                    {
                        "data" : "junos"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Base OS boot [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jbase"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Base OS Software Suite [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jcrypto"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Crypto Software Suite [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jcrypto-qfx-5"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Crypto Software Suite [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jdocs"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Online Documentation [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jkernel"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Kernel Software Suite [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jpfe"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Packet Forwarding Engine Support (qfx-ex-x86-32) [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jroute"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Routing Software Suite [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jsd"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS jsd [i386-17.3R1.10-jet-1]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jsdn-i386"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS SDN Software Suite [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jswitch"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Enterprise Software Suite [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jweb-ex"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Web Management Platform Package [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "py-base-i386"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS py-base-i386 [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "py-extensions-i386"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS py-extensions-i386 [17.3R1.10]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "jais"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "AI-Scripts [7.0R1.0]"
                    }
                    ]
                }, 
                {
                    "name" : [
                    {
                        "data" : "JUNOS Host Software"
                    }
                    ], 
                    "comment" : [
                    {
                        "data" : "JUNOS Host Software [17.3R1.10]"
                    }
                    ]
                }
                ]
            }
            ]
        }
        ]
    }
    ]
}
```
# pb_rest_calls.yml 

```
# ansible-playbook uri/pb_rest_calls.yml 

PLAY [make rest calls to junos devices] *******************************************************************************************************************************************

TASK [check if some ports are reachable on Junos devices] *************************************************************************************************************************
ok: [qfx5100-44] => (item=22)
ok: [qfx5100-44] => (item=830)
ok: [qfx5100-44] => (item=3000)

TASK [create device directories] **************************************************************************************************************************************************
ok: [qfx5100-44 -> localhost]

TASK [make rest call to QFX devices] **********************************************************************************************************************************************
changed: [qfx5100-44 -> localhost] => (item=get-software-information)
changed: [qfx5100-44 -> localhost] => (item=get-bgp-neighbor-information)

PLAY RECAP ************************************************************************************************************************************************************************
qfx5100-44                 : ok=3    changed=1    unreachable=0    failed=0   

```
```
# ls uri/qfx5100-44/
get-bgp-neighbor-information_output.json  get-software-information_output.json  rpc_output.json
```
```
