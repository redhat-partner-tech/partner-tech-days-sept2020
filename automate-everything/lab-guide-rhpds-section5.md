# Section 5 : Orchestration

Objective: In this section we will create a small scale orchestration where:

* A user launches a job to add a new web server into the load balancer pool
* The selection of the new web server is based on (Ansible Tower) survey
* The job will require an approval from the admin
* The admin gets the approval request notification via Slack
* Once approved, before adding the new web serve into the pool, the system will ensure httpd is installed and running
* If all goes well, the new web server will then be added to the load balancer pool

**JOB TEMPLATE**

A job template is a definition and set of parameters for running an Ansible job. Job templates are useful to execute the same job many times. So before running an Ansible Job from Tower you must create a Job Template that pulls together:

* Inventory: On what hosts should the job run?
* Credentials What credentials are needed to log into the hosts?
* Project: Where is the Playbook?
* What Playbook to use?

#### Step 1 - Creating Job Templates

Go to the Templates view, click the ![plus](https://lh5.googleusercontent.com/gTJqFBwbrTEitHRQe8XJqFyb9CVMGPYJ_9m9B9DPCflU9xaT-6tY6WwdT0cjI-SYvI6fTF7cO80eVs5kNYwHiWVn0wNcEPmcSIL_p_eWIvHiTunfGUhhQlM99l4mubgVVlMcZbkg) button and choose Job Template.

Tip: Remember that you can often click on magnfying glasses to get an overview of options to pick to fill in fields.


| Parameter 	| Value 	|
|-	|-	|
| Name 	| Ping a node 	|
| Job Type 	| Run 	|
| Inventory 	| Workshop Inventory 	|
| Project 	| Tech Day Project	|
| Playbook 	| automate-everything/lab-examples/ping-node1.yml 	|
| Credentials 	| Workshop Credentials 	|


Click SAVE

Launch the job and observe the output. This is just an example of a simple job to ping (Ansible ping, not network ping) node1.

Now let's create another Job Template.

| Parameter 	| Value 	|
|-	|-	|
| Name 	| Install Web Server 	|
| Job Type 	| Run 	|
| Inventory 	| Workshop Inventory 	|
| Project 	| Tech Day Project	|
| Playbook 	| automate-everything/lab-examples/apache.yml 	|
| Credentials 	| Workshop Credentials 	|
| Options 	| Tick the checkbox next to 'Enable Privilege Escalation' 	|


Click SAVE

You will not be able to launch this Job Template successfully until we create a survey that asks users to supply the value of the variable "my_hostname" defined in the playbook.

<https://github.com/redhat-partner-tech/partner-tech-days-sept2020/blob/master/automate-everything/lab-examples/apache.yml>

We will configure the survey in a bit, but for now let's look at workflow.

**WORKFLOW**

The basic idea of a workflow is to link multiple Job Templates together. They may or may not share inventory, playbooks or even permissions. The links can be conditional:

* If job template A succeeds, job template B is automatically executed afterwards
* But in case of failure, job template C will be run

And, workflows are not even limited to Job Templates, but can also include project or inventory updates. This enables new applications for Ansible Tower: different Job Templates can build upon each other. 

For example: the networking team creates playbooks with their own content, in their own Git repository and even targeting their own inventory, while the operations team also has their own repos, playbooks and inventory.

#### Step 2 - Creating Workflows

Let's try creating a workflow which combines the 2 job templates we have created.

First, let's set up the workflow. Workflows are configured in the Templates view, you might have noticed you can choose between Job Template and Workflow Template when adding a template.

![workflow add](https://lh3.googleusercontent.com/F2ppdy4CtaLLelF1izrUW3fh7JWH6k6BhsE1x7IySwyhLsVQhn9DVig3Xgsdsfks6S8Zgj-DHqagVURjCok1MnpOXEEXCHEmyIgRnUGofAx0Q8cUkX7366rMrZWC0ohMvdSETeQo)



-   Go to the Templates view and click the the green plus button. This time choose Workflow Template


| Name 	| Default Web Server to Prod 	|
| Organization 	| Default 	|
| Inventory 	| Workshop Inventory 	|


Click SAVE

After saving the template the Workflow Visualizer opens to allow you to build a workflow. You can later open the Workflow Visualizer again by using the button on the template details page.

-   Click on the START button, a new node opens. To the right you can assign an action to the node, you can choose between JOBS, PROJECT SYNC, INVENTORY SYNC and APPROVAL.

-   In this lab we'll link our two jobs together, so select the "Ping a node" job and click SELECT.

-   The node gets annotated with the name of the job. Hover the mouse pointer over the node, you'll see a red x, a green + and a blue chain-symbol appear.

-   Click the green + sign

-   Choose "Install Web Server" as the next Job

-   Leave Type set to On Success

-   Click SELECT

-   Click SAVE in the WORKFLOW VISUALIZER view

-   Click SAVE in the Workflow Template view



**SURVEYS**

The survey feature only provides a simple query for data - it does not support four-eye principles, queries based on dynamic data or nested menus.


#### Step 3 - Creating a Survey

On the "Deploy Web Server to Prod" workflow template, click the "Add Survey" button.

| Prompt 	| Please specify a hostname 	|
| Description 	| Make sure the hostname is correct! 	|
| Answer VAR Name 	| my_hostname 	|
| Answer Type 	| Text 	|
| Min Length 	| 1 	|
| Max Length 	| 100 	|


Click +ADD, then click SAVE

Then SAVE the workflow.

Launch the workflow and when the survey prompts you for a hostname, use node1 as the answer. Observe the workflow, click on DETAILS to see outputs.

**NOTIFICATIONS**

Now let's look at notifications. Ansible Tower supports quite a few ways of notifying users, e.g. email, Slack, etc. For the purpose of this exercise, let's use Slack (note: if you don't have a Slack account, don't worry about it, set this up anyway and save it. We need this for our exercise on Approval later.)


#### Step 4 - Creating a Notification

On the Ansible Tower screen, go to ADMINISTRATION -> Notifications.

Click the green plus sign to create a new notification template:

| Name 	| My Slack Notification 	|
| Organization 	| Default 	|
| Type 	| Slack 	|
| Dest Channel 	| #general 	|
| Token 	| <insert your Slack token here if you have one, else insert "12345" 	|


Click SAVE

Now let's go back to our workflow from the previous exercise (Deploy Web Server to Prod).

Click on NOTIFICATIONS button at the top area. And enable the approval button.

![](https://lh5.googleusercontent.com/Kg6K8NQSV3fRW2wtzpY1CFrMhGHGyboJJsDJhjB2KCf38fnanBPjOYf8-P_rOZEOfk8owsZbDdCjWsLKh3rc-tK6-zhAb5UexP3ofc9E53NvOob9Sis2__37HJpnA7DKhU3O4K8e)

Then click on PERMISSIONS button, click on the green plus sign button to add a permission. Add our webdev1 user to be able to execute the workflow:

![](https://lh4.googleusercontent.com/nVo5AILoLGw51S6qJn7vY5sZw3fBmacSfvb_Hho8A4jv73Tl7HbKq3umVIw5Oetllpo1LbTzDDuPBF6fvILBT9CnnuYy9VuUlxjue6rAx7m6s12dslVZ3z5QIicEd0MPeRM3jUa3)

Don't forget to click on SAVE.

**APPROVAL**

<blurb>

#### Step 4 - Creating an Approval

Now click on the DETAILS button to go back to your workflow main screen, then click on WORKFLOW VISUALIZER button.

Click on the START button to create a new "node". At the top pull down menu, choose "Approval".

| Name 	| Approval 	|
| Desc 	| My approval step 	|
| Run 	| Always 	|
| Convergence 	| ANY 	|


Click SAVE

You will have this as the result:

Now click on the Approval node, then click on the BLUE CHAIN button:

![](https://lh3.googleusercontent.com/85jJOF1jKVrKpmHRliXBQmyOeAmW977kAEFLH5JD9zDEOW2-JwstnaHae-k835zgm2nM1H5Oen4QDoRHFY7oJISrIVQMYuykyTJcfSqHZ8KloLhK3PD9qQ3XQhaWnihKOW9d3BAp)

Then click on "Ping a node":

![](https://lh3.googleusercontent.com/IjwLMWlO9tbF0rczBW1p7yWC9Xi1zWEY-PpZPagC0n6gLtOJDgCgIRk87AE9A__3Bn6EzYiKYHBPD70lG539De4YwnOrATeVIVMxxVhBuhAtf4tHdmQVKlyVS68wGIjvY91JvTfI)

Then SAVE

![](https://lh4.googleusercontent.com/5SRbyDKVC1aA1G6UJ0cGpDOXnhRAqYJELAkbPLcUQGqZnYDw_qyXtNLDAc-VUYasZxZdjluLZpDeEbkiVkSvxBwwO8Hak3XUdyLunjGRvBmUsDbf_uE-xjArAuAf_rTFcpjLcQvK)

Then SAVE again

After you save the workflow, open a different web browser if you have one on your computer, or simply open a new browser window in incognito mode, and login to Ansible Tower as user webdev1. And launch the workflow, again use node1 as an input to the survey. The workflow would ask the admin user for an approval.

Switch back to the browser where admin is logged in, notice the BELL notification at the top of the screen, open it:

![](https://lh6.googleusercontent.com/ofzw0W0R8UriVO27uf0gS_rx1QAaQr8UowzXfR8tIz3z1uqeFH_Q3MjIQYpGh_uBZIfhtJ3N01nonhsIZObqSIwdWPc8e_sBslID5zK18H1bjV1Aab9y0E9VFsu633-6_an-OVb9)

Go ahead and approve it.

**FINAL STEPS**

#### Step 5 - Tying it all together

Let's create one more Job Template that let users add a new web server into the load balancer pool.

|

Parameter

 |

Value

 |
|

NAME

 |

Add node to bigIP Pool

 |
|

JOB TYPE

 |

Run

 |
|

INVENTORY

 |

Workshop Inventory

 |
|

PROJECT

 |

Tech Day Project

 |
|

PLAYBOOK

 |

automate-everything/lab-examples/bigip-add-node.yml

 |
|

CREDENTIAL

 |

Workshop Credentials

 |
|

OPTIONS

 |\
 |

-   Click SAVE

Then go back to our workflow and add the above new job template as followed:

![](https://lh5.googleusercontent.com/NIrhL7X7xfkwQqJ2V-TTBrNwdawI8dMLtXqu9JNg4WNMHLN2YrNPiTnSYV0zNsnatVPTPVfq4CPYJ_iLqV5r3ZZpdC0hisd9hP-oSjvtYsrPMoTRFqxvjkbxnbn_qECdnnVSCl0k)

Next, edit the survey we created earlier for our workflow and add one more survey question:

|

PROMPT

 |

Please specify an IP address

 |
|

DESCRIPTION

 |

Make sure the IP is correct!

 |
|

ANSWER VAR NAME

 |

my_ipaddy

 |
|

ANSWER TYPE

 |

Text

 |
|

MIN LENGTH

 |

1

 |
|

MAX LENGTH

 |

16

 |

-   Click +ADD, then click SAVE

Then SAVE the workflow.

As user webdev1, launch the workflow to add node2 to the F5 load balancer pool. Make sure you supply the correct IP address for your node2. You can find this in your lab_invetory file via CLI, or in Ansible Tower. Example:

node2 ansible_host=3.95.15.12 ansible_user=ec2-user private_ip=172.16.14.216

Then verify if node2 has been added to your load balancer via the F5 web interface.

End of the lab..

