# Section 2 : Exploring the lab environment with Ansible Engine

Objective: A quick introduction to Ansible command line for students with no experience with Ansible. Those with prior knowledge of Ansible might breeze through this section.

![](https://lh6.googleusercontent.com/rTq7eX7I-sKhufhld9ICPjuQa2RtfMCfnEsYDXAbuIgQ2tzWb17sD_9hJCy_0va8kSLG46llSguFB74hn9POSmC56NomuIDxKCkWuZ594W_XGydxeFJ2Kv8jnEJxD0ujf_ejMHyZ)

#### Step 1

Login to your Ansible Control Node via SSH as student1 as per the instruction in Section 1. And navigate to the home directory.

```
[student1@ansible ~]$
```

#### Step 2

Run the ansible command with the --version command to look at what is configured:

```
[student1@ansible ~]$ ansible --version
ansible 2.6.2
  config file = /home/student1/.ansible.cfg
  configured module search path = [u'/home/student1/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, May  3 2017, 07:55:04) [GCC 4.8.5 20150623 (Red Hat 4.8.5-14)]
```

Note: The Ansible version you see might differ from the above output

This command gives you information about the version of Ansible, location of the executable, version of Python, search path for the modules and location of the ansible configuration file.

#### Step 3

Use the cat command to view the contents of the ansible.cfg file.

```
[student1@ansible ~]$ cat ~/.ansible.cfg
[defaults]
stdout_callback = yaml
connection = smart
timeout = 60
deprecation_warnings = False
host_key_checking = False
retry_files_enabled = False
inventory = /home/student1/lab_inventory/hosts
[persistent_connection]
connect_timeout = 200
command_timeout = 200
[student1@ansible ~]$
```

Note the following parameters within the ansible.cfg file:

-   inventory: shows the location of the ansible inventory being used

#### Step 4

The scope of a play within a playbook is limited to the groups of hosts declared within an Ansible inventory. Ansible supports multiple [inventory](http://docs.ansible.com/ansible/latest/intro_inventory.html) types. An inventory could be a simple flat file with a collection of hosts defined within it or it could be a dynamic script (potentially querying a CMDB backend) that generates a list of devices to run the playbook against.

In this lab you will work with a file based inventory written in the ini format. Use the cat command to view the contents of your inventory:

```
[student1@ansible ~]$ cat ~/lab_inventory/hosts
```


The output will look as follows with student1 being the respective student workbench:

```
[all:vars]
ansible_user=student1
ansible_ssh_pass=skfE9PXk41hgMs
ansible_port=22

[lb]
f5 ansible_host=3.235.9.14 ansible_user=admin private_ip=172.16.135.2 ansible_ssh_pass=skfE9PXk41hgMs

[control]
ansible ansible_host=3.237.237.173 ansible_user=ec2-user private_ip=172.16.98.96

[web]
node1 ansible_host=100.24.107.157 ansible_user=ec2-user private_ip=172.16.36.182
node2 ansible_host=3.235.57.122 ansible_user=ec2-user private_ip=172.16.150.46
```

Note that the IP addresses will be different in your environment.

#### Step 5

In the above output every [ ] defines a group. For example [web] is a group that contains the hosts host1 and host2.

Note: A group called all always exists and contains all groups and hosts defined within an inventory.

We can associate variables to groups and hosts. Host variables are declared/defined on the same line as the host themselves. For example for the host f5:

f5 ansible_host=34.199.128.69 ansible_user=admin private_ip=172.16.26.136 ansible_ssh_pass=admin

-   f5 - The name that Ansible will use. This can but does not have to rely on DNS

-   ansible_host - The IP address that ansible will use, if not configured it will default to DNS

-   ansible_user - The user ansible will use to login to this host, if not configured it will default to the user the playbook is run from

-   private_ip - This value is not reserved by ansible so it will default to a [host variable](http://docs.ansible.com/ansible/latest/intro_inventory.html#host-variables). This variable can be used by playbooks or ignored completely.

-   ansible_ssh_pass - The password ansible will use to login to this host, if not configured it will assume the user the playbook ran from has access to this host through SSH keys.

Does the password have to be in plain text? No, Red Hat Ansible Tower can take care of credential management in an easy to use web GUI or a user may use [ansible-vault](https://docs.ansible.com/ansible/latest/network/getting_started/first_inventory.html#protecting-sensitive-variables-with-ansible-vault)

Go back to the home directory, all the following exercises will be performed in the home directory.

```
[student1@ansible ~]$ cd ~
```
