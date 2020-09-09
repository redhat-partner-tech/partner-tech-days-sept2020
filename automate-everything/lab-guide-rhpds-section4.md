# Section 4 : Users, Inventories, Credentials, Projects

Objective: To prepare for the lab exercises in Section 4, in this section we will cover the fundamentals of Ansible Tower.

**Ansible Tower Users**

In Section 2 we learned a little bit about Ansible Engine, which is the actual command line executable of Ansible. As you saw in the exercises, you needed to login to the actual control node via ssh. This means if you need to give another user permission to use the control node, you need to give that person an ssh (shell) access. This is not preferred since it's less secure, tedious to manage, and not scalable.

Ansible Tower has a built-in Role Based Access Control (RBAC). For the purpose of this lab, we will use the built in authentication system.

There are three types of Tower Users:

**- Normal User:** Have read and write access limited to the inventory and projects for which that user has been granted the appropriate roles and privileges.

**- System Auditor:** Auditors implicitly inherit the read-only capability for all objects within the Tower environment.

**- System Administrator:** Has admin, read, and write privileges over the entire Tower installation.


#### Step 1 - Create User


In the Tower menu under ACCESS click Users (reference your email from RHPDS with Workshop: Workbench login info for student1)

Click the green plus button

Fill in the values for the new user:

| Parameter 	| Value 	|
|-	|-	|
| First Name 	| Peter 	|
| Last Name 	| Parker 	|
| Organization 	| Default 	|
| Email 	| wweb@example.com 	|
| Username 	| webdav1 	|
| Password 	| ansible123 	|
| Confirm Password 	| ansible123 	|
| User Type 	| Normal User 	|

Click SAVE

![](https://lh4.googleusercontent.com/Wy2t7vQf8sUquxu4omf9z8Df7TsXfVKyrALPzYf2eTNqgnQyQq79CON4I96GrZoB4OIb3OtZOh96OYg6LjeQ5xRt-p48aakh5ANllSmWKU57Y7wEKkr_VreMA9gyzb92TZZ4A3-f)

**Inventories**

This is the equivalent of an inventory file in Ansible Engine. There is a lot more to it (like dynamic inventories) but let's start with the basics. There will be one inventory, the Workshop Inventory. Click the Workshop Inventory then click the Hosts button.

The inventory information at ~/lab_inventory/hosts was pre-loaded into the Ansible Tower Inventory as part of the provisioning process.

Example:

![](https://lh3.googleusercontent.com/YdT0ONWJ56KfW9BvOVOcJGIHf_DzqIN0DJFvdFImKUuzB7qkh8boRySGBRnVZtr5qklbhsOeC_Q_PcLkf6P0hXkKEgNA4RuIWFaCXMQN9QZoWiPjBUE9y5jokCApRRWf2x0VMMhA)

**Credentials**

Now we will examine the credentials to access our managed hosts from Tower. As part of the provisioning process for this Ansible Workshop the Workshop Credential has already been setup.

In the RESOURCES menu choose Credentials. Now click on the Workshop Credential.

Note the following information:

|

Parameter

 |

Value

 |
|

Credential Type

 |

Machine- Machine credentials define ssh and user-level privilege escalation access for playbooks. They are used when submitting jobs to run playbooks on a remote host.

 |
|

username

 |

ec2-user which matches our command-line Ansible inventory username for the other linux nodes

 |
|

SSH PRIVATE KEY

 |

ENCRYPTED - take note that you can't actually examine the SSH private key once someone hands it over to Ansible Tower

 |

**Projects**

An Ansible Tower Project is a logical collection of Ansible Playbooks. You can manage your playbooks by placing them into a source code management (SCM) system supported by Tower, including Git, Subversion, and Mercurial.

For this demonstration we will use playbooks stored in a Git repository:

https://github.com/redhat-partner-tech/partner-tech-days-sept2020

To configure and use this repository as a Source Control Management (SCM) system in Tower you have to create a Project that uses the repository

Go to RESOURCES → Projects in the side menu view click the green + button. Fill in the form:

|

Parameter

 |

Value

 |
|

NAME

 |

Tech Day Project

 |
|

ORGANIZATION

 |

Default

 |
|

SCM TYPE

 |

Git

 |

Now you need the URL to access the repo. Go to the Github repository mentioned above, choose the green Clone or download button on the right, click on Use https and copy the HTTPS URL. Enter the URL into the Project configuration:

|

Parameter

 |

Value

 |
|

SCM URL

 |

https://github.com/redhat-partner-tech/partner-tech-days-sept2020

 |
|

SCM UPDATE OPTIONS

 |

Tick the first three boxes to always get a fresh copy of the repository and to update the repository when launching a job

 |

Click SAVE

The new Project will be synced automatically after creation. But you can also do this manually: Sync the Project again with the Git repository by going to the Projects view and clicking the circular arrow Get latest SCM revision icon to the right of the Project.

After starting the sync job, go to the Jobs view: there is a new job for the update of the Git repository.
