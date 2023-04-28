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

* Attaching the Internet Gateway to the VPC

![internet-gateway-attach](./img/8-attach.PNG)

* Editing a route in public route table, and associating it with the Internet Gateway. (This is what allows a public subnet to be accessible from the Internet).

![route](./img/21-route.PNG)

* Creating 3 Elastic IPs

![eleastic-ip](./img/22-elastic-ip.PNG)


* Creating a Nat Gateway and assigning one of the Elastic IPs to it

![nat-gateway](./img/23-nat.PNG)

* Setting up Security Group for Application load balancer in a way that it will allow https traffic from any IP address.

![sg-alb](./img/25-sg-alb.PNG)

* Setting up Security Group for Nginx servers in a way that it will allow https traffic only from the Application Load balancer and allow bastion servers to ssh into it.

![sg-nginx](./img/27-sg-nginx.PNG)


* Setting up Security Group for bastion server in a way that it will only allow access from the workstations that need to SSH into the bastion servers.

![sg-bastion](./img/26-sg-bastion.PNG)

* Setting up Security Group for internal Application Load balancer in a way that it will allow traffic only from Nginx servers

![sg-int](./img/28-sg-int.PNG)

* Setting up Security Group for Webservers in a way that it will allow https traffic only from the internal Application Load balancer


![sg-webserver](./img/29-webserver.PNG)


* Setting Security Group for the Data Layer subnet in a way that it will allow TCP port 3306 traffic only from the webservers.

![route](./img/30-sg-datalayer.PNG)


* Elastic File System (EFS) must be carefully desinged – only webservers should be able to connect to RDS 


![efs](./img/33-efs.PNG)
![efs2](./img/34-efs2.PNG)
![efs3](./img/35-efs3.PNG)
![efs4](./img/36-efs4.PNG)
![efs5](./img/37-efs5.PNG)

* Nginx and Webservers will have access to EFS Mountpoint.

![ap](./img/38-ap.PNG)
![ap2](./img/39-ap2.PNG)
![ap3](./img/40-ap3.PNG)
![ap4](./img/41-ap4.PNG)
![ap5](./img/42-ap5.PNG)

### STEP 3: Creating An AMI Out Of The EC2 Instance For Nginx And Bastion server

* 3 EC2 Instance based on Red Hat of the T2 micro family were launched for Nginx, bastion and the one for the two webservers

![ec2](./img/31-ec2.PNG)
* Creating an AMI out of the Bastion EC2 instance

* For The Baston Server
After connecting to it through ssh on the terminal, the following commands are run to install some necessary softwares:


![sudo](./img/32-sudo.PNG)
![sudo2](./img/33-sudo2.PNG)
![sudo3](./img/34-sudo3.PNG)
![sudo4](./img/35-sudo4.PNG)






For Nginx Server

* After connecting to EC2 Instance for the Nginx through ssh on the terminal, the following commands are run to install some necessary softwares:

![nginx2](./img/36-nginx2.PNG)
![nginx3](./img/37-nginx3.PNG)
![nginx4](./img/38-nginx4.PNG)
![nginx5](./img/39-nginx5.PNG)
![nginx6](./img/40-nginx5.PNG)
![nginx6](./img/41-nginx6.PNG)

![ssl](./img/42-ssl-cert.PNG)
![dhpharm](./img/43-dhpharm.PNG)
![dhpharm2](./img/44-dhparam.PNG)
![private](./img/45-private.PNG)

* Creating an AMI out of the Nginx EC2 instance

![nginx](./img/41-nginx-ami.PNG)


### STEP 4: Creating An AMI Out Of The EC2 Instance For The Tooling and Wordpress Webservers

* After connecting to the EC2 instance for the tooling and wordpress site through SSH on the terminal, the following commands are run to install some necessary softwares:

![web](./img/66-web.PNG)
![web2](./img/67-web2.PNG)
![web3](./img/68-web3.PNG)
![web4](./img/69-web4.PNG)
![web5](./img/70-web5.PNG)
![web6](./img/71-web6.PNG)
![web7](./img/72-web7.PNG)

![ssl](./img/73-ssl.PNG)
![ssl2](./img/74-ssl2.PNG)
![ssl3](./img/75-ssl3.PNG)

* Creating an AMI out of the EC2 instance

![web-ami](./img/76-web-ami.PNG)

![web-ami](./img/79-ami.PNG)
### STEP 5: Creating A Launch Template

**For Bastion Server** 

* Setting up a launch template with the Bastion AMI
* Ensuring the Instances are launched into the public subnet
* Entering the Userdata to update yum package repository and install ansible and mysql

![temp](./img/105-temp.PNG)
![temp2](./img/106-temp2.PNG)
![temp3](./img/107-temp3.PNG)
![temp4](./img/108-temp4.PNG)
![temp5](./img/109-temp5.PNG)
![temp6](./img/110-temp6.PNG)
![temp7](./img/111-temp7.PNG)


**For Nginx Server**

* Setting up a launch template with the Nginx AMI
* Ensuring the Instances are launched into the public subnet
* Assigning appropriate security group
* Entering the Userdata to update yum package repository and install Nginx


![nginx-temp](./img/112-nginx-temp.PNG)
![nginx-temp2](./img/113-nginx-temp2.PNG)
![nginx-temp3](./img/114-nginx-temp3.PNG)
![nginx-temp4](./img/115-nginx-temp4.PNG)


**For Wordpress Server**

* Selecting Instances as the target type
* Ensuring the protocol HTTPS on secure TLS port 443
* Ensuring that the health check path is /healthstatus

![web-temp](./img/116-web-temp.PNG)
![web-temp2](./img/117-web-temp2.PNG)
![web-temp3](./img/118-web-temp3.PNG)
![web-temp4](./img/119-web-temp4.PNG)
![web-temp5](./img/120-web-temp5.PNG)


**For Tooling Server**

* Setting up a launch template with the Bastion AMI
* Ensuring the Instances are launched into the public subnet
* Assigning appropriate security group
* Entering the Userdata to update yum package repository and apache server

![tooling-temp](./img/121-tooling-temp.PNG)
![tooling-temp2](./img/122-tooling-temp2.PNG)
![tooling-temp3](./img/123-tooling-temp3.PNG)
![tooling-temp4](./img/124-tooling-temp4.PNG)
![tooling-temp5](./img/125-tooling-temp5.PNG)
![tooling-temp6](./img/126-tooling-temp6.PNG)
![tooling-temp7](./img/121-tooling-temp7.PNG)


### STEP 6: Configuring Target Groups

For Nginx Server

* Selecting Instances as the target type
* Ensuring the protocol HTTPS on secure TLS port 443
* Ensuring that the health check path is /healthstatus

![nginx-target](./img/80-nginx-target.PNG)
![nginx-target2](./img/81-nginx-target2.PNG)


For Tooling Server

* Selecting Instances as the target type
* Ensuring the protocol HTTPS on secure TLS port 443
* Ensuring that the health check path is /healthstatus


For Wordpress Server

* Selecting Instances as the target type
* Ensuring the protocol HTTPS on secure TLS port 443
* Ensuring that the health check path is /healthstatus

![target](./img/82-target.PNG)


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


**For Bastion Server**
![asg](./img/128-asg.PNG)
![asg1](./img/129-asg-1.PNG)
![asg2](./img/130-asg-2.PNG)
![asg3](./img/131-asg-3.PNG)
![asg4](./img/132-asg-4.PNG)
![asg5](./img/133-asg-5.PNG)
![asg6](./img/134-asg-6.PNG)
![asg7](./img/135-asg-7.PNG)
![asg8](./img/136-asg-8.PNG)
![asg9](./img/137-asg-9.PNG)
![asg10](./img/138-asg-10.PNG)

**For Nginx Server**

![asg-nginx1](./img/139-asg-nginx-1.PNG)
![asg-nginx2](./img/140-asg-nginx-2.PNG)
![asg-nginx3](./img/141-asg-nginx-3.PNG)
![asg-nginx4](./img/142-asg-nginx-4.PNG)
![asg-nginx5](./img/143-asg-nginx-5.PNG)
![asg-nginx6](./img/144-asg-nginx-6.PNG)
![asg-nginx7](./img/145-asg-nginx-7.PNG)
![asg-nginx8](./img/146-asg-nginx-8.PNG)


**For Wordpress**

![wordpress](./img/149-wordpress.PNG)
![wordpress2](./img/150-wordpress2.PNG)
![wordpress3](./img/151-wordpress3.PNG)
![wordpress4](./img/152-wordpress4.PNG)
![wordpress5](./img/153-wordpress5.PNG)
![wordpress6](./img/154-wordpress6.PNG)
![wordpress7](./img/155-wordpress7.PNG)
![wordpress8](./img/156-wordpress8.PNG)


**For Tooling Server**

![tooling](./img/157-tooling.PNG)
![tooling2](./img/158-tooling2.PNG)
![tooling3](./img/159-tooling3.PNG)
![tooling4](./img/160-tooling4.PNG)
![tooling5](./img/161-tooling5.PNG)
![tooling6](./img/162-tooling6.PNG)
![tooling7](./img/163-tooling7.PNG)
![tooling8](./img/164-tooling8.PNG)

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