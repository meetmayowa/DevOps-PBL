## AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY

### INTRODUCTION

This project demostrates how a secure infrastructure inside AWS VPC (Virtual Private Cloud) network is built for a particular company, who uses WordPress CMS for its main business website, and a Tooling Website for their DevOps team. As part of the company’s desire for improved security and performance, a decision has been made to use a reverse proxy technology from NGINX to achieve this. The infrastructure will look like following diagram:

![tooling](./img/1-tooling_project_15.PNG)

The following outlines the steps taken:

### STEP 1: Setting Up a Sub-account And Creating A Hosted Zone
* Creating a sub-account 'DevOps' from my AWS master account in the AWS Organisation Unit console

![devops](./img/2-devops.PNG)

* Creating an AWS Organization Unit (OU) 'Dev' within the Root account(Dev resources will be launched in there)


![dev](./img/3-dev.PNG)

* Moving the DevOps account into the Dev OU

![move](./img/4-move.PNG)


* Logging in to the newly created AWS account

![roles](./img/5-roles.PNG)

* Creating a hosted zone in the Route 53 console and mapping it to the domain name acquired from freenom.


![route53](./img/6-route53.PNG)

### STEP 2: Setting Up a Virtual Private Network (VPC)

* Creating a VPC from the VPC console
![route53](./img/6-vpc.PNG)
* Creating subnets for the public and private resources as shown in the architecture
![route53](./img/9-sub.PNG)

![sub1](./img/10-sub1.PNG)
![sub2](./img/11-sub2.PNG)
![sub3](./img/12-sub3.PNG)
![sub4](./img/13-sub4.PNG)
![sub5](./img/14-sub5.PNG)
![sub6](./img/15-sub6.PNG)
![all-sub](./img/16-all-sub.PNG)

* Creating a route table and associating it with public subnets

![rtb1](./img/17-rtb1.PNG)

![public](./img/19-public.PNG)

* Creating a route table and associating it with private subnets

![rtb1](./img/18-rtb2.PNG)

![private](./img/20-private.PNG)


* Creating an Internet Gateway

![internet-gateway](./img/7-igw.PNG)

* Editing a route in public route table, and associating it with the Internet Gateway. (This is what allows a public subnet to be accessible from the Internet).

![route](./img/21-route.PNG)

* Creating 3 Elastic IPs

![route](./img/21-route.PNG)bf
![route](./img/21-route.PNG)fbf

* Creating a Nat Gateway and assigning one of the Elastic IPs to it

![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc

* Setting up Security Group for Application load balancer in a way that it will allow https traffic from any IP address.

![route](./img/21-route.PNG)bcbc

* Setting up Security Group for Nginx servers in a way that it will allow https traffic only from the Application Load balancer and allow bastion servers to ssh into it.

![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc

* Setting up Security Group for bastion server in a way that it will only allow access from the workstations that need to SSH into the bastion servers.

![route](./img/21-route.PNG)bcbc

* Setting up Security Group for internal Application Load balancer in a way that it will allow traffic only from Nginx servers

![route](./img/21-route.PNG)bcbc

* Setting up Security Group for Webservers in a way that it will allow https traffic only from the internal Application Load balancer


![route](./img/21-route.PNG)bcbc

* Setting up Security Group for Webservers in a way that it will allow https traffic only from the internal Application Load balancer


![route](./img/21-route.PNG)bcbc


![route](./img/21-route.PNG)bcbc


* Setting Security Group for the Data Layer subnet in a way that it will allow TCP port 3306 traffic only from the webservers.

![route](./img/21-route.PNG)bcbc


### STEP 3: Creating An AMI Out Of The EC2 Instance For Nginx And Bastion server

* 3 EC2 Instance based on Red Hat of the T2 micro family were launched for Nginx, bastion and the one for the two webservers

![route](./img/21-route.PNG)bcbc

* For The Baston Server
After connecting to it through ssh on the terminal, the following commands are run to install some necessary softwares:

![route](./img/21-route.PNG)bcbc

![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc




### STEP 4: Creating An AMI Out Of The EC2 Instance For The Tooling and Wordpress Webservers

* After connecting to the EC2 instance for the tooling and wordpress site through SSH on the terminal, the following commands are run to install some necessary softwares:

![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc

* Creating an AMI out of the EC2 instance

### STEP 5: Creating A Launch Template

**For Bastion Server** 

* Setting up a launch template with the Bastion AMI
* Ensuring the Instances are launched into the public subnet
* Entering the Userdata to update yum package repository and install ansible and mysql

![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc


**For Nginx Server**

* Setting up a launch template with the Nginx AMI
* Ensuring the Instances are launched into the public subnet
* Assigning appropriate security group
* Entering the Userdata to update yum package repository and install Nginx


![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc


**For Tooling Server**

* Setting up a launch template with the Bastion AMI
* Ensuring the Instances are launched into the public subnet
* Assigning appropriate security group
* Entering the Userdata to update yum package repository and apache server

![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc

**For Wordpress Server**

* Selecting Instances as the target type
* Ensuring the protocol HTTPS on secure TLS port 443
* Ensuring that the health check path is /healthstatus

![route](./img/21-route.PNG)bcbc


### STEP 7: Configuring AutoScaling Group
* Selecting the right launch template
* Selecting the VPC
* Selecting both public subnets
* Enabling Application Load Balancer for the AutoScalingGroup (ASG)
* Selecting the target group you created before
* Ensuring health checks for both EC2 and ALB
* Setting the desired capacity, Minimum capacity and Maximum capacity to 2
* Setting the scale out option if CPU utilization reaches 90%
* Activating SNS topic to send scaling notifications

**For Nginx Server**

![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc


**For Bastion Server**
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc

**For Tooling Server**

![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc

**For Wordpress**

![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc
![route](./img/21-route.PNG)bcbc

### STEP 8: Creating TLS Certificates From Amazon Certificate Manager (ACM)


TLS certificates is created to handle secured connectivity to the Application Load Balancers (ALB)

* Navigating to AWS ACM
* Requesting a public wildcard certificate for the domain name I registered in www.smartweb.com.ng
* Using DNS to validate the domain name

![cert](./img/85-cert.PNG)
![cert2](./img/86-cert2.PNG)
![cert4](./img/88-cert4.PNG)
![cert-record](./img/89-cert-record.PNG)
![cert-issued](./img/90-cert-issued.PNG)

### STEP 9: Configuring Application Load Balancer (ALB)

**For External Load Balancer**

* Selecting Internet facing option
* Ensuring that it listens on HTTPS protocol (TCP port 443)
* Ensuring the ALB is created within the appropriate VPC, AZ and the right Subnets
* Choosing the Certificate already created from ACM
* Selecting Security Group for the external load balancer
* Selecting Nginx Instances as the target group


![ALB](./img/91-ALB.PNG)

![ALB1](./img/91-ALB1.PNG)

![ALB2](./img/92-ALB2.PNG)

![ALB3](./img/93-ALB3.PNG)

![ALB4](./img/94-ALB4.PNG)

![ALB5](./img/95-ALB5.PNG)

![ALB6](./img/96-ALB-sucess.PNG)


**For Internal Load Balancer**

* Setting the Internal ALB option
* Ensuring that it listens on HTTPS protocol (TCP port 443)
* Ensuring the ALB is created within the appropriate VPC, AZ and Subnets
* Choosing the Certificate already created from ACM
* Selecting Security Group for the internal load balancer
* Selecting webserver Instances as the target group
* Ensuring that health check passes for the target group

![int](./img/97-int.PNG)

![int2](./img/98-int2.PNG)

![int3](./img/99-int3.PNG)

![int4](./img/100-int4.PNG)

![int5](./img/101-int5.PNG)

![int6](./img/102-int6.PNG)

* Configuring the Listener on the Internal ALB to route traffic to wordpress server


![cert](./img/103-rules.PNG)

![cert](./img/104-rules2.PNG)

### STEP 10: Setting Up EFS Storage For The Webservers
* Create an EFS filesystem
* Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer
* Associate the Security groups created earlier for data layer.
* Create an EFS access point. (Give it a name and leave all other settings as default)

![cert](./img/85-cert.PNG)

![cert](./img/85-cert.PNG)

### STEP 11: Creating Key Management Service(KMS)


![ksm](./img/43-ksm.PNG)

![ksm2](./img/44-ksm2.PNG)

![ksm3](./img/45-ksm3.PNG)

![ksm33](./img/46-ksm3.PNG)

![ksm4](./img/47-ksm4.PNG)



### STEP 12: Setting Up A Relational Database System


![rds](./img/50-rds.PNG)

![rds2](./img/51-rds2.PNG)


### STEP 13: Creating DNS Records In The Route53 For the Tooling And Wordporess site

**For Tooling DNS record**


![cert](./img/85-cert.PNG)

**For Wordpress record**

![cert](./img/85-cert.PNG)


### STEP 14: Result
* Entering the url https://www.imayorstudios.com.ng will route traffic from the application load balancer to the nginx and then to the server for wordpress site through the internal ALB:

![cert](./img/85-cert.PNG)


Entering the url https://www.imayorstudios.com.ng will route traffic from the application load balancer to the nginx and then to the internal ALB which forwards the traffic to the server for tooling site:


![cert](./img/85-cert.PNG)


Done!