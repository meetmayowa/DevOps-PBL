# AUTOMATING INFRASTRUCTURE WITH IAC USING TERRAFORM PART 4 - TERRAFORM CLOUD
  

## INTRODUCTION
In this project, instead of running the Terraform codes in project 18 from a command line, rather it is being executed from Terraform cloud console. The AMI is built differently with packer while Ansible is used to configure the infrastructure after its been provisioned by Terraform.

The following outlines the steps:

## STEP 1: Setting Up A Terraform Account

* Create an Organization
* After verifying my email and creating an organisation in the terraform cloud site, then on the configure workspace page, selecting **version control workflow** option inorder to run Terraform commands triggered from my git repository.
* Creating a new repository called terraform-cloud and pushing my terraform codes developed in project 18 into the repository
* Connecting the workspace to the new repository created and clicking on **create workspace**


![workspace](./img/2-Organ.PNG)
![workspace](./img/7-gitlab.PNG)
![workspace](./img/8-workspace.PNG)
![workspace](./img/9-workspace2.PNG)

* Clicking on configure variables on the next page to setup my AWS credentials as environment variables.

![workspace](./img/10-variables.PNG)

![workspace](./img/11-access_key.PNG)

* Now my Terraform cloud is all set to apply the codes from GitHub and create all necessary AWS resources.


### STEP 2: Building AMI With Packer

* Installing packer on my local machine:

* Cloning the repository and changing directory to the AMI folder

* Running the packer commands to build AMI for Bastion server, Nginx server and webserver

For Bastion Server

![packer](./img/12-packer-bas.PNG)

![packer](./img/13-packer-bas2.PNG)

![packer](./img/14-packer-bas3.PNG)

![packer](./img/15-packer-bas4.PNG)

![packer](./img/16-packer-bas5.PNG)

For Nginx Server

![packer](./img/17-packer-nginx.PNG)

![packer](./img/18-packer-nginx2.PNG)

For Tooling and Wordpress Server

![packer](./img/19-packer-web.PNG)

![packer](./img/20-packer-web2.PNG)


For Jenkins, Artifactory and sonarqube Server

![packer](./img/21-packer-ubuntu.PNG)

![packer](./img/22-packer-ubuntu2.PNG)


![ami](./img/23-ami-packer.PNG)
### STEP 3: Running The Terraform Cloud To Provision Resources

* Inputing the AMI ID in my terraform.tfvars file for the servers built with packer which terraform will use to provision Bastion, Nginx, Tooling and Wordpress server

![rds](./img/23-rds.PNG)

* Pushing the codes to my repository will cause terraform cloud to trigger a plan

* Accepting the plan to to trigger an apply command

![plan](./img/24-plan.PNG)

![plan2](./img/25-plan2.PNG)