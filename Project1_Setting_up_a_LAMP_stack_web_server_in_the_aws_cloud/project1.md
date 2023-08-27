# LAMP STACK IMPLEMENTATION 
**By Mayowa Ipinyomi**

Date: 10/09/2022

Table of Contents
* 1- Step 1 - Installing Apache and updating the firewall
* 2- Step 2 - Installing Mysql
* 3- Step 3 - Installing PHP
* 4- Step 4 - Creating a virtualhost for your website your Apache
* 5- Step 5 - Enable PHP on the website

## STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL   
Apache is an open source software available for free. The Apache web server is among the most popular web servers in the world. To install the Apache on the Ubuntu OS, we shall carry out the following steps; 

1- Install Apache using Ubuntu’s package manager `‘apt’`:

Start by updating the package manager cache to update the software repository on the server. 

#update a list of packages in package manager
`sudo apt update`

![apache](./img/1-update.PNG)


2- Then, install Apache with:
#run apache2 package installation
`sudo apt install apache2`

![apache](./img/2-apache.PNG)

3- You’ll also be prompted to confirm Apache’s installation by pressing `Y`, then ENTER.

4-To verify that apache2 is running as a Service in our OS, use following command
#verify that apache2 is running
`sudo systemctl status apache2`

![reload](./img/3-reload.PNG)

**Note: If it is green and running, then you did everything correctly**

5- Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is the default port that web browsers use to access web pages on the Internet.

Therefore, using the AWS Management Console, we shall configure it to open the port 80 for the Apache to run. 



6- First, let us try to check how we can access it locally in our Ubuntu shell, run:
`curl http://localhost:80  or  curl http://127.0.0.1:80`

![port80](./img/4-port80.PNG)

7- Now it is time for us to test how our Apache HTTP server can respond to requests from the Internet. Open a web browser of your choice and try to access following url

`http://<Public-IP-Address>:80`

![default](./img/5-default.PNG)

## Step 2 — Installing MySQL
Now that you have a web server up and running, you need to install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project. 

1- Again, use `‘apt’` to acquire and install this software:

`$ sudo apt install mysql-server`


![mysql](./img/6-mysql.PNG)

2-When prompted, confirm installation by typing `Y`, and then ENTER

When the installation is finished, log in to the MySQL console by typing:

`$ sudo mysql`
This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command

![mysql](./img/7-sudo-mysql.PNG)