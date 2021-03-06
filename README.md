ACL Role for Dell EMC Networking OS
======================================

This role facilitates the configuration of an access control list (ACL). It supports the configuration of different types of ACLs (standard and extended) for both IPv4 and IPv6, and assigns the access-class to the line terminals. This role is abstracted for dellos9.


Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-acl
```

Requirements
------------
This role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in OS connection variables or the ``provider`` dictionary.

Role Variables
--------------

This role is abstracted using the ``ansible_net_os_name`` variable that can take the following value "dellos9".

Any role variable with a corresponding state variable set to absent negates the configuration of that variable. For variables with no state variable, setting an empty value for the variable negates the corresponding configuration. The variables and its values are case-sensitive.

``dellos_acl`` contains the following keys:

|        Key | Type                      | Notes                                                                                                                                                                                     |
|------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| type | string (required) choices: ipv4, ipv6, mac        | Configures the L3 (IPv4/IPv6) or L2 (MAC) access control list.                                                                                                             |
|name| string (required)           | Configures the name of the access control list.                                 |
|description| string           | Configures the description about the access control list.                             |
| remark | list           | Configures remark for ACL. See the following remark.* keys for each list item.                                                                                           |
| remark.number | interger (required)        | Configures the remark number.                                           |
| remark.description | string        | Configures description of the remark.                                          |
| remark.state | string, choices: absent, present*   | If set to absent, deletes the configured remark for ACL entry.                                                                                                         |
|extended| boolean: true, false         | If set to true, configures extended type ACL; if set to false, configures standard ACL. This variable is not supported for IPv6.                                                                   |
| entries | list | Configures rules in the access control list. See the following seqlist.* keys for each list item. |
| entries.number | int (required)       | Specifies the sequence number of the rule in access control list.          |
| entries.permit| boolean (required): true, false         | If set to true, specifies the rule to permit packets; if set to false, specifies to reject packets.                                                                |
| entries.protocol| string (required)       | Specifies the type of protocol or the protocol number to filter.          |
| entries.source| string (required)        | Specifies the source address to match in the packets.                                                                                                                                             |
| entries.src_condition| string         | Specifies the condition to filter packets from the source address. This key is ignored when the type is MAC.                                                                                                |
|entries.destination| string (required)      | Specifies the destination address to match in the packets.            |
|entries.dest_condition| string         | Specifies the condition to filter packets to the destination address.          |
|entries.other_options| string         | Specifies the other options applied on packets. For eg count,log,order,monitor..etc.         |
|entries.state | string, choices: absent, present*   | If set to absent, deletes the rule from the access control list.  |
|stage_ingress | list           | Configures ingress ACL to the interface. See the following stage_ingress.* keys for each list item.                                                                                           |
| stage_ingress.name | string (required)        | Configures the ingress ACL filter to the interface with this interface name.                                                             |
| stage_ingress.state | string, choices: absent, present*   | If set to absent, deletes the configured ACL from the interface.                                                                                                         |
| stage_egress | list           | Configures egress ACL to the interface. See the following stage_egress.* keys for each list item.                                                                       |
| stage_egress.name | string (required)        | Configures the egress ACL filter to the interface with this interface name.                                                                                                        |
| stage_egress.state | string, choices: absent, present*   | If set to absent, deletes the configured egress ACL from the interface.                                                                                                         |
| lineterminal | list | Configures the terminal to apply the access control list. See the following lineterminal.* keys for each list item. |
| lineterminal.line | string (required)      | Configures access class on the line terminal.        |
| lineterminal.state | string, choices: absent, present*       | If set to absent, deletes the access class from line terminal.                                                                                           |
| state | string, choices: absent, present*       | If set to absent, deletes the access control list.                                                                                                                                                                |

```
Note: Asterisk (*) denotes the default value if none is specified. 
```

Connection Variables
--------------------

Ansible Dell EMC Networking roles require the following connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible group_vars or host_vars directories, or in the playbook itself.



|         Key | Required | Choices    | Description                              |
| ----------: | -------- | ---------- | ---------------------------------------- |
|        host | yes      |            | Hostname or address for connecting to the remote device over the specified ``transport`` variable. The value of this key is the destination address for the transport. |
|        port | no       |            | Port used to build the connection to the remote device. If the value of this key is unspecified, the value defaults to 22. |
|    username | no       |            | Configures the username that authenticates the connection to the remote device. The value of this key authenticates the CLI login. If this key does not specify a value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password that authenticates the connection to the remote device. If this key does not specify a value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTHORIZE is used instead. If not specified, the device attempts to execute all commands in non-privileged mode.|
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If ``authorize`` is set to no, then this key is not applicable. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. This key supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all of the above connection arguments as a dictionary object. All constraints (such as required or choices) must be met either by individual arguments or values in this dictionary. |


```
Note: Asterisk (*) denotes the default value if none is specified.
```

Dependencies
------------

The dellos-acl role is built on modules included in the core Ansible code. These modules were added in Ansible version 2.2.0.

Example Playbook
----------------

The following example uses the dellos-acl role to configure different types of ACLs (standard and extended) for both IPv4 and IPv6 and assigns the access-class to the line terminals. The example creates a ``hosts`` file with the switch details and corresponding variables. The hosts file should define the variable `` ansible_net_os_name `` with corresponding Dell EMC networking OS name. It writes a simple playbook that only references the dellos-acl role. 

Sample ``hosts`` file:
 
    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9)>

Sample ``host_vars/leaf1``:

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: xxxxx 
      password: xxxxx
      authorize: yes
      auth_pass: xxxxx 
      transport: cli
    dellos_acl:
      - type: ipv4
        name: ssh-only
        description: ipv4acl
        extended: true
        remark:
          - number: 5
            description: "ipv4remark"
        entries:
          - number: 5
            permit: true
            protocol: tcp
            source: any
            src_condition: ack
            destination: any
            dest_condition: eq 22
            other_options: count
            state: present
        stage_ingress:
          - name: fortyGigE 1/28
            state: present
        stage_egress:
          - name: fortyGigE 1/28
            state: present
        lineterminal:
          - line: vty 1
            state: present
          - line: vty 2
            state: absent
        state: present
            
Simple playbook to setup system, ``leaf.yaml``:

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-acl

Then run with:

    ansible-playbook -i hosts leaf.yaml
    
License
-------

Copyright (c) 2017, Dell Inc. All rights reserved.
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
    http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
