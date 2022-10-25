# AUTOMATING PROJECTS WITH ANSIBLE CONFIGURATION MANAGEMENT

## INTRODUCTION
In this project, the Jenkins server from project 9 Web Architecture is setup and configured to function as a Jump Server or Bastion Host by using Ansible Configuration Management which helps to automate most of repetitive tasks done in previous projects.

## Task
* Install and configure Ansible client to act as a Jump Server/Bastion Host
* Create a simple Ansible playbook to automate servers configuration


The following outlines the steps I took in configuring an Ansible server and also configuring a build task in Jenkins job that is linked to my Github repository to trigger whenever there is a change in the main branch by the use of webhooks, of which the artifacts from the build are used to automate tasks with Ansible playbook.

## STEP 0: Connecting To The Jenkins Server
I updated the tag name of the `Jenkins server` to `Jenkins-Ansible` and connecting to it from my terminal via ssh connection and also starting the whole servers setup in project 7-8 for ansible playbook to perform task on them.


![ec2](./img/1-ec2.PNG)



STEP 1: Configuring Ansible In The Jenkins-Ansible Server