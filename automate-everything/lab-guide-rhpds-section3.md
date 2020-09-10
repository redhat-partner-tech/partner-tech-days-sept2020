# Section 3 : Quick intro to F5 Load Balancer

Objective: To cover the basics of a load balancer, in this case an F5 BigIP is used as an example of a load balancer and a network device that Ansible can control and automate.

#### Step 1 - Gather Facts

Using your text editor of choice create a new file called bigip-facts.yml.

```
[student1@ansible ~]$ nano bigip-facts.yml
```

vim and nano are available on the control node, as well as Visual Studio

Ansible playbooks are YAML files. YAML is a structured encoding format that is also extremely human readable (unlike it's subset - the JSON format).

Enter the following play definition into bigip-facts.yml:

```
---
- name: GRAB F5 FACTS
  hosts: f5
  connection: local
  gather_facts: no
```

-   The --- at the top of the file indicates that this is a YAML file.

-   The hosts: f5, indicates the play is run only on the F5 BIG-IP device

-   connection: local tells the Playbook to run locally (rather than SSHing to itself)

-   gather_facts: no disables facts gathering. We are not using any fact variables for this playbook.

Next, add the first task. This task will use the bigip_device_info module to grab useful information from the BIG-IP device.

```
 tasks:
- name: COLLECT BIG-IP FACTS
      bigip_device_info:
        gather_subset:
- system-info
        provider:
          server: "{{private_ip}}"
          user: "{{ansible_user}}"
          password: "{{ansible_ssh_pass}}"
          server_port: 8443
          validate_certs: no
      register: device_facts
```

A play is a list of tasks. Tasks and modules have a 1:1 correlation. Ansible modules are reusable, standalone scripts that can be used by the Ansible API, or by the ansible or ansible-playbook programs. They return information to ansible by printing a JSON string to stdout before exiting.

-   name: COLLECT BIG-IP FACTS is a user defined description that will display in the terminal output.
-   bigip_device_info: tells the task which module to use. Everything except register is a module parameter defined on the module documentation page.
-   The gather_subset: system_info parameter tells the module only to grab system level information.
-   The provider: parameter is a group of connection details for the BIG-IP.
-   The server: "{{private_ip}}" parameter tells the module to connect to the F5 BIG-IP IP address, which is stored as a variable private_ip in inventory
-   The user: "{{ansible_user}}" parameter tells the module the username to login to the F5 BIG-IP device with
-   Thepassword: "{{ansible_ssh_pass}}" parameter tells the module the password to login to the F5 BIG-IP device with
-   The server_port: 8443 parameter tells the module the port to connect to the F5 BIG-IP device with. 8443 is what's being used in this lab, but could be different depending on the deployment.
-   register: device_facts tells the task to save the output to a variable bigip_device_info

Next, append the second task to above . This task will use the debug module to print the output from device_facts variable we registered the facts to.

```
- name: DISPLAY COMPLETE BIG-IP SYSTEM INFORMATION
      debug:
        var: device_facts
```

-   The name: COMPLETE BIG-IP SYSTEM INFORMATION is a user defined description that will display in the terminal output.
-   debug: tells the task to use the debug module.
-   The var: device_facts parameter tells the module to display the variable bigip_device_info.

Finally let's append two more tasks to get more specific info from facts gathered, to the above playbook.

```
- name: DISPLAY ONLY THE MAC ADDRESS
      debug:
        var: device_facts['system_info']['base_mac_address']
- name: DISPLAY ONLY THE VERSION
      debug:
        var: device_facts['system_info']['product_version']
```

-   var: device_facts['system_info']['base_mac_address'] displays the MAC address for the Management IP on the BIG-IP device
-   device_facts['system_info']['product_version'] displays the product version BIG-IP device

Because the bigip_device_info module returns useful information in structured data, it is really easy to grab specific information without using regex or filters. Fact modules are very powerful tools to grab specific device information that can be used in subsequent tasks, or even used to create dynamic documentation (reports, csv files, markdown).

Run the playbook - exit back into the command line of the control host and execute the following:

```
[student1@ansible ~]$ ansible-playbook bigip-facts.yml
```

The output will look as follows.

```
[student1@ansible ~]$ ansible-playbook bigip-facts.yml
PLAY [GRAB F5 FACTS] ****************************************************************************************************************************************
TASK [COLLECT BIG-IP FACTS] *********************************************************************************************************************************
changed: [f5]
TASK [DISPLAY COMPLETE BIG-IP SYSTEM INFORMATION] ***********************************************************************************************************
ok: [f5] => {
    "device_facts": {
        "changed": true,
        "failed": false,
        "system_info": {
            "base_mac_address": "0a:54:53:51:86:fc",
            "chassis_serial": "685023ec-071e-3fa0-3849dcf70dff",
            "hardware_information": [
                {
                    "model": "Intel(R) Xeon(R) CPU E5-2676 v3 @ 2.40GHz",
                    "name": "cpus",
                    "type": "base-board",
                    "versions": [
                        {
                            "name": "cpu stepping",
                            "version": "2"
                        },
                        {
                            "name": "cpu sockets",
                            "version": "1"
                        },
                        {
                            "name": "cpu MHz",
                            "version": "2399.981"
                        },
                        {
                            "name": "cores",
                            "version": "2  (physical:2)"
                        },
                        {
                            "name": "cache size",
                            "version": "30720 KB"
                        }
                    ]
                }
            ],
            "marketing_name": "BIG-IP Virtual Edition",
            "package_edition": "Point Release 7",
            "package_version": "Build 0.0.1 - Tue May 15 15:26:30 PDT 2018",
            "platform": "Z100",
            "product_build": "0.0.1",
            "product_build_date": "Tue May 15 15:26:30 PDT 2018",
            "product_built": 180515152630,
            "product_changelist": 2557198,
            "product_code": "BIG-IP",
            "product_jobid": 1012030,
            "product_version": "13.1.0.7",
            "time": {
                "day": 15,
                "hour": 23,
                "minute": 46,
                "month": 4,
                "second": 25,
                "year": 2019
            },
            "uptime": 1738.0
        }
    }
}
TASK [DISPLAY ONLY THE MAC ADDRESS] *************************************************************************************************************************
ok: [f5] => {
    "device_facts['system_info']['base_mac_address']": "0a:54:53:51:86:fc"
}
TASK [DISPLAY ONLY THE VERSION] *****************************************************************************************************************************
ok: [f5] => {
    "device_facts['system_info']['product_version']": "13.1.0.7"
}
PLAY RECAP **************************************************************************************************************************************************
f5 : ok=4    changed=1    unreachable=0    failed=0
```

And finally, please confirm that you have web access to your F5 load balancer. You can find its details in your Ansible inventory file.
For example, for the entry below, you would go to [https://34.199.128.69:8443](http://34.199.128.69:8443) (the web interface listens to port 8443), username is admin, password is skfE9PXk41hgMs

```
[lb]
f5 ansible_host=34.199.128.69 ansible_user=admin private_ip=172.16.26.136 ansible_ssh_pass=skfE9PXk41hgMs
```

#### Step 2 - Add nodes

![](https://lh5.googleusercontent.com/wHMFdI0ck1jokElILTUE9HgARY1Yorn38vN74SjPPxME52ZPOmqUbsZX8BtFr1xD6YbSIprCR2FaI7GEIdV5dt_C-X-ijUvsRfoX6yb9CQz3aDH4v9IqOJacaqLCsKeKnsHrGiEy)

Using your text editor of choice create a new file called bigip-node.yml.

```
[student1@ansible ~]$ nano bigip-node.yml
```

vim and nano are available on the control node, as well as Visual Studio and Atom via RDP

Enter the following play definition into bigip-node.yml:

```
---
- name: BIG-IP SETUP
  hosts: lb
  connection: local
  gather_facts: false
  tasks:

- name: CREATE NODES
    bigip_node:
      provider:
        server: "{{private_ip}}"
        user: "{{ansible_user}}"
        password: "{{ansible_ssh_pass}}"
        server_port: 8443
        validate_certs: no
      host: "{{hostvars[item].ansible_host}}"
      name: "{{hostvars[item].inventory_hostname}}"
    loop: "{{ groups['web'] }}"
```

-   The --- at the top of the file indicates that this is a YAML file.

-   The hosts: lb, indicates the play is run only on the lb group. Technically there only one F5 device but if there were multiple they would be configured simultaneously.

-   connection: local tells the Playbook to run locally (rather than SSHing to itself)

-   gather_facts: false disables facts gathering. We are not using any fact variables for this playbook.

-   name: CREATE NODES is a user defined description that will display in the terminal output.

-   bigip_node: tells the task which module to use. Everything except loop is a module parameter defined on the module documentation page.

-   The server: "{{private_ip}}" parameter tells the module to connect to the F5 BIG-IP IP address, which is stored as a variable private_ip in inventory

-   The provider: parameter is a group of connection details for the BIG-IP.

-   The user: "{{ansible_user}}" parameter tells the module the username to login to the F5 BIG-IP device with

-   The password: "{{ansible_ssh_pass}}" parameter tells the module the password to login to the F5 BIG-IP device with

-   The server_port: 8443 parameter tells the module the port to connect to the F5 BIG-IP device with

-   The host: "{{hostvars[item].ansible_host}}" parameter tells the module to add a web server IP address already defined in our inventory.

-   The name: "{{hostvars[item].inventory_hostname}}" parameter tells the module to use the inventory_hostname as the name (which will be node1 and node2).

-   The validate_certs: "no" parameter tells the module to not validate SSL certificates. This is just used for demonstration purposes since this is a lab.

-   loop: tells the task to loop over the provided list. The list in this case is the group web which includes two RHEL hosts.

Run the playbook - exit back into the command line of the control host and execute the following:

```
[student1@ansible ~]$ ansible-playbook bigip-node.yml
```

The output will look as follows.

```
[student1@ansible]$ ansible-playbook bigip-node.yml

PLAY [BIG-IP SETUP] ************************************************************
TASK [CREATE NODES] ************************************************************
changed: [f5] => (item=node1)
changed: [f5] => (item=node2)
PLAY RECAP *********************************************************************
f5 : ok=1    changed=1    unreachable=0    failed=0
```

Confirm that you can see the nodes added in F5 by logging in to F5 via the web interface, and going to Local Traffic → Nodes

![](https://lh3.googleusercontent.com/0ov5DUZQYZA4Nr8zje_JQ30-30aBhMBvJEZbGGdRv6-V7vYllVbL5FeufNI2gm9-w0d1RKJYkIeQhEe587HTKf1PBI3OYNNLenTvB3bNl-b8DByBM7Dz1NWjxebJgPULAo1pw4yg)

#### Step 3 - Create a pool and add members

Enter the following play definition into bigip-pool.yml:

```
---
- name: BIG-IP SETUP
  hosts: lb
  connection: local
  gather_facts: false
  tasks:

- name: CREATE POOL
    bigip_pool:
      provider:
        server: "{{private_ip}}"
        user: "{{ansible_user}}"
        password: "{{ansible_ssh_pass}}"
        server_port: 8443
        validate_certs: no
      name: "http_pool"
      lb_method: "round-robin"
      monitors: "/Common/http"
      monitor_type: "and_list"
```

-   The --- at the top of the file indicates that this is a YAML file.

-   The hosts: lb, indicates the play is run only on the lb group. Technically there only one F5 device but if there were multiple they would be configured simultaneously.

-   connection: local tells the Playbook to run locally (rather than SSHing to itself)

-   gather_facts: false disables facts gathering. We are not using any fact variables for this playbook.

-   name: CREATE POOL is a user defined description that will display in the terminal output.

-   bigip_pool: tells the task which module to use.

-   The server: "{{private_ip}}" parameter tells the module to connect to the F5 BIG-IP IP address, which is stored as a variable private_ip in inventory

-   The provider: parameter is a group of connection details for the BIG-IP.

-   The user: "{{ansible_user}}" parameter tells the module the username to login to the F5 BIG-IP device with

-   The password: "{{ansible_ssh_pass}}" parameter tells the module the password to login to the F5 BIG-IP device with

-   The server_port: 8443 parameter tells the module the port to connect to the F5 BIG-IP device with

-   The name: "http_pool" parameter tells the module to create a pool named http_pool

-   The lb_method: "round-robin" parameter tells the module the load balancing method will be round-robin. A full list of methods can be found on the documentation page for bigip_pool.

-   The monitors: "/Common/http" parameter tells the module that the http_pool will only look at http traffic.

-   The monitor_type: "and_list" ensures that all monitors are checked.

-   The validate_certs: "no" parameter tells the module to not validate SSL certificates. This is just used for demonstration purposes since this is a lab.

Run the playbook - exit back into the command line of the control host and execute the following:
```
[student1@ansible ~]$ ansible-playbook bigip-pool.yml
```
The output will look as follows.
```
[student1@ansible ~]$ ansible-playbook bigip-pool.yml

PLAY [BIG-IP SETUP] ************************************************************
TASK [CREATE POOL] *************************************************************
changed: [f5]
PLAY RECAP *********************************************************************
f5 : ok=1    changed=1    unreachable=0    failed=0
```

Now that the pool has been created, let's add node1 and node2 into the pool.

Enter the following play definition into bigip-pool-members.yml:
```
---
- name: BIG-IP SETUP
  hosts: lb
  connection: local
  gather_facts: false
  tasks:

- name: ADD POOL MEMBERS
    bigip_pool_member:
      provider:
        server: "{{private_ip}}"
        user: "{{ansible_user}}"
        password: "{{ansible_ssh_pass}}"
        server_port: 8443
        validate_certs: no
      state: "present"
      name: "{{hostvars[item].inventory_hostname}}"
      host: "{{hostvars[item].ansible_host}}"
      port: "80"
      pool: "http_pool"
    loop: "{{ groups['web'] }}"
```
-   The --- at the top of the file indicates that this is a YAML file.

-   The hosts: lb, indicates the play is run only on the lb group. Technically there only one F5 device but if there were multiple they would be configured simultaneously.

-   connection: local tells the Playbook to run locally (rather than SSHing to itself)

-   gather_facts: false disables facts gathering. We are not using any fact variables for this playbook.

-   name: ADD POOL MEMBERS is a user defined description that will display in the terminal output.

-   bigip_pool_member: tells the task which module to use.

-   The server: "{{private_ip}}" parameter tells the module to connect to the F5 BIG-IP IP address, which is stored as a variable private_ip in inventory

-   The provider: parameter is a group of connection details for the BIG-IP.

-   The user: "{{ansible_user}}" parameter tells the module the username to login to the F5 BIG-IP device with

-   The password: "{{ansible_ssh_pass}}" parameter tells the module the password to login to the F5 BIG-IP device with

-   The server_port: 8443 parameter tells the module the port to connect to the F5 BIG-IP device with

-   The state: "present" parameter tells the module we want this to be added rather than deleted.

-   The name: "{{hostvars[item].inventory_hostname}}" parameter tells the module to use the inventory_hostname as the name (which will be host1 and host2).

-   The host: "{{hostvars[item].ansible_host}}" parameter tells the module to add a web server IP address already defined in our inventory.

-   The port: parameter tells the pool member port.

-   The pool: "http_pool" parameter tells the module to put this node into a pool named http_pool

-   The validate_certs: "no" parameter tells the module to not validate SSL certificates. This is just used for demonstration purposes since this is a lab. Finally there is a loop parameter which is at the task level (it is not a module parameter but a task level parameter:

-   loop: tells the task to loop over the provided list. The list in this case is the group web which includes two RHEL hosts.

Run the playbook - exit back into the command line of the control host and execute the following:
```
[student1@ansible ~]$ ansible-playbook bigip-pool-members.yml
```
The output will look as follows.
```
[student1@ansible ~]$ ansible-playbook bigip-pool-members.yml

PLAY [BIG-IP SETUP] ************************************************************
TASK [ADD POOL MEMBERS] ********************************************************
changed: [f5] => (item=host1)
changed: [f5] => (item=host2)
PLAY RECAP *********************************************************************
f5 : ok=1    changed=1    unreachable=0    failed=0
```
Confirm that you see the new pool created and its members by logging in to your F5 load balancer web interface.

![](https://lh6.googleusercontent.com/CUS6O9IILzxLVVp8NCT6m2i8gzfH3neZoFjdDAnNisdRnT0psqCuK8KDiY1xPeZF-OY_gpPrE1d0BLKTt2O5hEMmb9fvN_ek6nT5lU7PXU6TVfvLqw7KNCOSA3R2uQ-XetCWw3fa)

![](https://lh3.googleusercontent.com/DBpyEF0xV_Z6qsnM6GGO3rDKIn72O0uRNKFGx9Rn1KWO_XMUR44Pat0VwXPJA7yc8KafwLo_aL1VxVR3YpNlDnmI7rw--OQCmagytiJe_c5SVSBklpnYNs_4B0NeWwkoqOfiodKl)

#### Step 4 - Create a virtual server

A virtual server in F5 load balancer is a combination of IP and port number. When an incoming web request comes to this virtual server, the request will then be passed to the pool of nodes that is assigned to that virtual server. Who gets to answer the requests? That depends on the load balancing policy chosen, for example if "round robin" is used, each member node of the pool will take turn to answer requests. (Remember, we did set our pool to use round robin)

Now let's create a virtual server.

Enter the following play definition into bigip-virtual-server.yml:
```
---
- name: BIG-IP SETUP
  hosts: lb
  connection: local
  gather_facts: false
  tasks:

- name: ADD VIRTUAL SERVER
    bigip_virtual_server:
      provider:
        server: "{{private_ip}}"
        user: "{{ansible_user}}"
        password: "{{ansible_ssh_pass}}"
        server_port: 8443
        validate_certs: no
      name: "vip"
      destination: "{{private_ip}}"
      port: "443"
      enabled_vlans: "all"
      all_profiles: ['http','clientssl','oneconnect']
      pool: "http_pool"
      snat: "Automap"
```
-   The --- at the top of the file indicates that this is a YAML file.

-   The hosts: f5, indicates the play is run only on the F5 BIG-IP device

-   connection: local tells the Playbook to run locally (rather than SSHing to itself)

-   gather_facts: no disables facts gathering. We are not using any fact variables for this playbook.

-   name: ADD VIRTUAL SERVER is a user defined description that will display in the terminal output.

-   bigip_virtual_server: tells the task which module to use.

-   The server: "{{private_ip}}" parameter tells the module to connect to the F5 BIG-IP IP address, which is stored as a variable private_ip in inventory

-   The provider: parameter is a group of connection details for the BIG-IP.

-   The user: "{{ansible_user}}" parameter tells the module the username to login to the F5 BIG-IP device with

-   Thepassword: "{{ansible_ssh_pass}}" parameter tells the module the password to login to the F5 BIG-IP device with

-   The server_port: 8443 parameter tells the module the port to connect to the F5 BIG-IP device with

-   The name: "vip" parameter tells the module to create a virtual server named vip

-   The destination" parameter tells the module which IP address to assign for the virtual server

-   The port paramter tells the module which Port the virtual server will be listening on

-   The enabled_vlans parameter tells the module which all vlans the virtual server is enbaled for

-   The all_profiles paramter tells the module which all profiles are assigned to the virtuals server

-   The pool parameter tells the module which pool is assigned to the virtual server

-   The snat paramter tells the module what the Source network address address should be. In this module we are assigning it to be Automap which means the source address on the request that goes to the backend server will be the self-ip address of the BIG-IP

-   The validate_certs: "no" parameter tells the module to not validate SSL certificates. This is just used for demonstration purposes since this is a lab.

Run the playbook - exit back into the command line of the control host and execute the following:
```
[student1@ansible ~]$ ansible-playbook bigip-virtual-server.yml

PLAY [BIG-IP SETUP]*************************************************************
TASK [ADD VIRTUAL SERVER] ******************************************************
changed: [f5]
PLAY RECAP *********************************************************************
f5 : ok=1    changed=1    unreachable=0    failed=0
```
Confirm that you see the new virtual server in F5:

![](https://lh3.googleusercontent.com/4wVTJOohN3TxDqBGBYkknVqial1Tb06AGt9pGIBrFJuKXa6TZ1BYfdsFXtSn-ntlTyTZyM7XRkdtxdQ-NQ95mDy8z6JrFl7KKrojJwLL_ScE_0HtphwPECYS7G8A2qlRwZWoVnb6)

Verify that the load balancing does really work:

Each RHEL web server actually already has apache running. The above exercises have successfully set up the load balancer for the pool of web servers. Open up the public IP of the F5 load balancer in your web browser:

This time use port 443 instead of 8443, e.g. [https://X.X.X.X:443/](https://x.x.x.x/)

Each time you refresh the host will change between node1 and node2. Here is animation of the host field changing: ![animation](https://lh5.googleusercontent.com/JGdU0EnwzgPCWiBo4KMnDuRTxJBvRrkWnqErzeScPGvf3BX4T6Kzx_w3IRiKR088jSUnMP_TqWp3B8H78hR2fPmFdQ5LdyRhqEC1kig-NwMg9nnXnjg7SZNLWNVXvVWloBD0wjum)

Instead of using a browser window it is also possible to use the command line on the Ansible control node. Use the curl command on the ansible_host to access public IP or private IP address of F5 load balancer in combination with the --insecure and --silent command line arguments. Since the entire website is loaded on the command line it is recommended to | grep for the student number assigned to the respective workbench. (e.g. student5 would | grep student5)
```
[studentX@ansible ~]$ curl https://172.16.26.136:443 --insecure --silent | grep studentX

    <p>F5TEST-studentX-node1</p>

[studentX@ansible ~]$ curl https://172.16.26.136:443 --insecure --silent | grep studentX

    <p>F5TEST-studentX-node2</p>

[studentX@ansible ~]$ curl https://172.16.26.136:443 --insecure --silent | grep studentX

    <p>F5TEST-studentX-node1</p>
```

As the final step in this section, let’s remove node2 from F5 so that we can use it for the exercises in Section 5. To do this, please use the F5 web interface:
1. Go to Local Traffic → Pools → Members
2. Click on node2, then [Remove]
3. Local Traffic → Nodes 
4. Click on node2, then [Delete]. Confirm Delete
