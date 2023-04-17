# AUTOMATING INFRASTRUCTURE WITH IAC USING TERRAFORM PART 4 - TERRAFORM CLOUD
  

## INTRODUCTION
In this project, instead of running the Terraform codes in project 18 from a command line, rather it is being executed from Terraform cloud console. The AMI is built differently with packer while Ansible is used to configure the infrastructure after its been provisioned by Terraform.

The following outlines the steps:

## STEP 1: Setting Up A Terraform Account
* After verifying my email and creating an organisation in the terraform cloud site, then on the configure workspace page, selecting **version control workflow** option inorder to run Terraform commands triggered from my git repository.
* Creating a new repository called terraform-cloud and pushing my terraform codes developed in project 18 into the repository
* Connecting the workspace to the new repository created and clicking on **create workspace**


![ec2](./img/1-plan.PNG)
