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


