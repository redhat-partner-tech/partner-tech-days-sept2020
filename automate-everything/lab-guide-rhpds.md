WORK IN PROGRESS

#### LAB GUIDE - Ansible on RHPDS

#### Section 1 : Standing up the lab environment

Objective: Instructions on how to stand up the lab

#### Section 2 : Exploring the lab environment

Objective: A quick introduction to Ansible command line for students with no experience with Ansible. Those with prior knowledge of Ansible might breeze through this section.

#### Section 3 : Quick intro to F5 Load Balancer

Objective: To cover the basics of a load balancer, in this case an F5 BigIP is used as an example of a load balancer and a network device that Ansible can control and automate.

#### Section 4 : Users, Inventories, Credentials, Projects

Objective: To prepare for the lab exercises in Section 4, in this section we will cover the fundamentals of Ansible Tower.

#### Section 5 : Users, Inventories, Credentials, Projects

Objective: In this section we will create a small scale orchestration where:

-   A user launches a job to add a new web server into the load balancer pool

-   The selection of the new web server is based on (Ansible Tower) survey

-   The job will require an approval from the admin

-   The admin gets the approval request notification via Slack

-   Once approved, before adding the new web server into the pool, the system will ensure httpd is installed and running

-   If all goes well, the new web server will then be added to the load balancer pool

#### Section 1 : Standing up the lab environment in RHPDS

Objective: We will be using a modified version of the Ansible F5 Automation workshop in RHPDS for the exercises, let's set it up.

REQUIREMENTS

These lab exercises requires a few things:

-   You need an access to Red Hat Product Demo System (RHPDS) located at <https://rhpds.redhat.com>, please contact your Red Hat account manager to get one

-   A computer with a full direct access to the Internet, note: connecting through corporate VPN network has been known to cause issues in accessing the lab

-   SSH client such as Putty on Windows or terminal (x-term) on Linux or Mac

LAB SETUP

1.  Login to RHPDS (<https://rhpds.redhat.com>)

2.  Go to the Services -> Catalogs. Then, under Service Catalogs > All Services > Workshops choose: Ansible F5 Automation Workshop

![](https://lh3.googleusercontent.com/jduBS4LHNAyIsFoEj3iYusGuYb11cV8yHW8lgg5YNupl8L8y996IEVUwLQdBr09ev1Zz3G9w2M0AE_BUQ7gCWSzrUhYDV-COa2P4AC7G0c6kaSxjz6drHeHk5rEAS_2ySr8-36Mq)

1.  Click the Order button

2.  Fill out the Lab form 

-   Set "SFDC Opportunity, Campaign ID, or Partner Registration" = 7013a000002gvgSAAQ

-   Note: Make sure to select the appropriate region, for example North America if you are in North America

-   Leave Number of Attendees = 1

4.  Click the Submit button

Once you click the Submit button, you should receive a confirmation email within 5-10 minutes that your lab is being prepared. It would take 30-90 minutes for the lab to be ready to use. When it is ready, you will receive a final email with an instruction on how to access your lab. For example:

Thank you for ordering Ansible Linux Automation Workshop (T)

Your environment for RHPDS-RH-arwinata-redhat.com-PROD_ANSIBLE_WORKSHOPS-4fb0_COMPLETED has been provisioned.

<... snip ...>

NOTICE: Your environment will expire and be deleted in 2 day(s) at 2020-09-03 00:00:00 -0400.

The list of VMs for this workshop is available at:[  http://f544.open.redhat.com](http://4fb0.open.redhat.com/)

And when you go to the web link provided, you should see something as followed:

![](https://lh5.googleusercontent.com/c1klrdAyiYL-BYTljBQXSJw3QP9T0mh0ZlJNFl2FhHy8Pcy4xm-leFdQGFDL0168pm0B9MSKln4tX9zWMckxgaykqh59DUcBrLxt0SSMdypnqYrW24p5j6R6Hl-kyE2DDTqIEVf8)

Scroll down to the bottom and click on student1 under Workbench Information to see details on how to access your lab. Example:

![](https://lh5.googleusercontent.com/FNyXO1DesMn24kORQkBPHdRyDST-yhlUZNMpRlYRu5mcGg3JPRywnHLeU9EcztR5r7mZvPMi9JTfbQSV1SwCBhfxYiNNkMlh-OsmQB1f6f2m4dY_yaO0I2ruQMf5B9vL37fdv-If)

1.  Follow the instructions above to access your Ansible Control Node via SSH

By now your lab is ready to use for the exercises in this lab guide. Please make sure to follow only the exercise instructions in this lab guide, and *not* the one on the web link (example above [http://f544.open.redhat.com](http://4fb0.open.redhat.com/)) you receive from RHPDS.


