### DEPLOYING A SIMPLE BOOK REGISTER APPLICATION WITH MEAN STACK IN AWS CLOUD

**MEAN** Stack is a combination of following components:
**MongoDB (Document database)** – Stores and allows to retrieve data.
**Express (Back-end application framework)** – Makes requests to Database for Reads and Writes.
**Angular (Front-end application framework)** – Handles Client and Server Requests
**Node.js (JavaScript runtime environment)** – Accepts requests and displays results to end user

## STEP 1: Preparing Prerequisite - Setting up a virtual server in the cloud (Using AWS)
To setup a virtual server, I Created a new EC2 Instance of t2.nano family with Ubuntu Server 20.04 LTS (HVM) image from AWS account. 

After a successful launch of the EC2 instance(ubuntu server), I connected to the EC2 instance from Windpws terminal with my private key(.pem file).

## STEP 2: Installing NodeJS

The following commands are used to configure the ubuntu server:

* Update ubuntu : `$ sudo apt update`
* Upgrade ubuntu: `$ sudo apt upgrade` 

![sudo-apt-upgrade](./images/40-update-upgrade.PNG) 


* Adding certificates: `$ sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates` 
* To get the location of Node.js software from Ubuntu repositories: `$ curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash –`
* Installing NodeJS: `$ sudo apt install –y nodejs` 