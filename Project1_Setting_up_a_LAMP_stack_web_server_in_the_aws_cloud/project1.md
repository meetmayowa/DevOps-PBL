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

3- Before running the script you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as `PassWord.1`


`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`


![mysql2](./img/8-mysql2.PNG)

4-Start the interactive script by running:

5- If you enabled password validation, you’ll be shown the password strength for the root password you just entered and your server will ask if you want to continue with that password. If you are happy with your current password, enter `Y`
 for `“yes”` at the prompt:

![scripts](./img/9-scripts.PNG)


6- When you’re finished, test if you’re able to log in to the MySQL console by typing:
 `$ sudo mysql -p`
 `$ mysql -u root -p`

![root](./img/10-root.PNG)

![pass](./img/11-pass.PNG)

### STEP 3 — INSTALLING PHP

You have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the PHP package, you’ll needphp-mysql

1 - a PHP module that allows PHP to communicate with MySQL-based databases. You’ll also need  to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.
To install these 3 packages at once, 

run:`Libapache2-mod-php`

![root](./img/12-php.PNG)

2-Version Confirmation 

`php -v`

![version](./img/13-version.PNG)

At this point, your LAMP stack is completely installed and fully operational.
