# IMPLEMENTATION OF WEB APPLICATION ARCHITECTURE WITH A SINGLE DATABASE AND NFS SERVER

## BACKGROUND
In this project, I implemented a DevOps tooling website solution which makes access to DevOps tools with the following components:

1- Infrastructure: AWS

2- 3 Linux Webservers: Red Hat Enterprise Linux 8 that will serve the DevOps tooling website

3- A Database Server: Ubuntu 20.04 for reads and write

4- A Storage Server: Red Hat Enterprise Linux 8 that will serve as NFS Server for storing shared files that the 3 Web Servers will use
5- Programming Language: PHP
Code Repository: GitHub

The following are the steps I took to set up this 3-tier 
Web Application Architecture with a single database and an NFS server as a shared file storage:

## STEP 1: PREPARE NFS SERVER

1 - Launching a four (4) EC2 instance(Red Hat Enterprise Linux 8 HVM) where one will serve as "NFS server” and while the  remaining will three (3) we be user for "Web-servers" which will be connected to the NFS server to make it stateless.

   ![ec2](./img/1-ec2.PNG)

2 - Creating 3 EBS volumes in the same Availability Zone (us-east-1c) with my EC2 instance created.
  
   ![ec2](./img/1-ec2.PNG)

* Attach a volume each to an instance to use it as you would a regular physical hard disk drive.

![ec2](./img/1-ec2.PNG)

PARTITIONING THE VOLUMES ATTACHED TO THE EC2 INSTANCE AND CREATING LOGICAL VOLUME WITH IT.

Open the Linux terminal to start the configuration and partitioning.

Partitioning the volumes attached to the Ec2 Instance and creating Logical volume with It

After a successful SSH connection to the EC2 instance on my terminal, running the following commands:

* To inspect what block devices are attached to the server: `lsblk`
* To see all mounts and free space: `df –h`

![ec2](./img/1-ec2.PNG)

* Creating a single partition on each of the 3 disks:
* For xdvf disk: `sudo gdisk /dev/xvdf`
* Entering `‘n’` key to add a new partition
* Accepting all the defaults settings and selecting Linux LVM type of partition by entering 8300 code and hit enter
* Entering the `‘w’` key to write the new partition created and confirming with the `‘Y’` and hit enter.

![ec2](./img/2-lvm.PNG)

* I followed the same process to create single partition for the xvdg and xvdh drives respectively

* Use lsblk utility to view the newly configured partition on each of the 3 disks. `lsblk`

![ec2](./img/2-lvm.PNG)

* Installing LVM2 package for creating logical volumes on a linux server using this command: sudo yum install lvm2

![ec2](./img/2-lvm.PNG)

Note: Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.

* Checking for available partitions: `sudo lvmdiskscan`

![ec2](./img/2-lvm.PNG)

* Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM: `sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

![ec2](./img/2-lvm.PNG)

* Check if all the physical voulumes have been created properly


![ec2](./img/2-lvm.PNG)

* Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg: `sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

* Verify that my VG has been created successfully by running:  `sudo vgs`


* On the Volume group, we can now create our Logical Volume (db-lv)



![ec2](./img/2-lvm.PNG)

* On the Volume group, we can now create our Logical Volume (lv-apps, lv-logs and lv-opt)

* Use lvcreate utility to create 3 logical volumes. lv-apps, and lv- logs and lv-opt. Use the remaining space of the PV size. 

NOTE: 

`lv-apps` was used by the webserver, 

`lv-logs` was used by webserver logs while 

`lv-opt` will be used by Jenkins server in Project 8.


![ec2](./img/2-lvm.PNG)

* To display the entire setup, use this command: `sudo vgdisplay -v`

![ec2](./img/2-lvm.PNG)

* And also use this command: `sudo lsblk`  to display the entire setup

![ec2](./img/2-lvm.PNG)


### FORMATTING AND MOUNTING THE LOGICAL VOLUMES

We are going to make the 2 Logical Volume a filesystem

* Formatting the 2 logical volumes with ext4 filesystem:

`sudo mkfs -t xfs /dev/webdata-vg/lv-apps`

`sudo mkfs -t xfs /dev/webdata-vg/lv-logs`

`sudo mkfs -t xfs /dev/webdata-vg/lv-opts`

![ec2](./img/2-lvm.PNG)


* Next is to create a mount point for our devices

* Creating a directory where the website files will be stored: `sudo mkdir –p /var/www/html`

* Create /home/recovery/logs to store backup of log data: `sudo mkdir -p /home/recovery/logs`

* Mounting apps-lv logical volume on /var/www/html: `sudo mount /dev/webdata-vg/apps-lv /var/www/html`

* Check the log to make sure it is empty, if it is not then you will need to back it up. Use this command to check: `sudo ls -l /var/log`

![ec2](./img/2-lvm.PNG)


* Using rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs: sudo rsync –av /var/log/. /home/recovery/logs/

![ec2](./img/2-lvm.PNG)



* Use this command line to confirm that it has been backed up: `sudo ls -l /home/recovery/logs/log`

* We can go ahead and mount on var/log directory: `sudo mount /dev/webdata-vg/logs-lv /var/log`

* Its very important we restore back the files that we backup earlier into var/log directory by using the following command: `sudo rsync -av /home/recovery/logs/. /var/log`

![ec2](./img/2-lvm.PNG)


* Locate the UUID of the device that will be used to update the /etc/fstab file and copy it: `sudo blkid`


![ec2](./img/2-lvm.PNG)


* Updating the fstab file so that mount configuration will persist after restart of the server:

`sudo vi /etc/fstab`

* Test the configuration: sudo mount -a

* Reload the daemon: sudo systemctl daemon-reload

* Verify your setup by running `df -h`, output must look like this:


![ec2](./img/2-lvm.PNG)

**3 - Create mount points on /mnt directory for the logical volumes as follow:**

* Mount lv-apps on /mnt/apps – To be used by webservers
* Mount lv-logs on /mnt/logs – To be used by webserver logs
* Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8

![ec2](./img/2-lvm.PNG)

**4 - Install NFS server, configure it to start on reboot and make sure it is u and running**


```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```


![lvm](./img/2-lvm.PNG)


* Export the mounts for webservers’ subnet cidr to connect as clients.


![ec2](./img/1-ec2.PNG)

* Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:

```
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt

sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service

```


![ec2](./img/2-lvm.PNG)

* Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 ):

```
sudo vi /etc/exports

/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

Esc + :wq!

sudo exportfs -arv

```


**6 - Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)**

`rpcinfo -p | grep nfs`


![ec2](./img/2-lvm.PNG)


* Important note: In order for NFS server to be accessible from our client, we must also open following ports: TCP 111, UDP 111, UDP 2049


![ec2](./img/1-ec2.PNG)


## STEP 2: CONFIGURE THE DATABASE SERVER

* Configuring the database server: `sudo yum update`

* Installing MySQL: `sudo yum install mysql-server`

![ec2](./img/2-lvm.PNG)

* Verify that the service is up and running by using `sudo systemctl status mysqld`

![ec2](./img/2-lvm.PNG)

**CONFIGURING MYSQL DBMS TO WORK WITH REMOTE WEBSERVER**

* Activating the mysql shell: `sudo mysql`

* Enter Mysql Password: `sudo mysql -u root -p`

* Creating a database called ‘tooling’: `mysql> CREATE DATABASE tooling;`

* Creating a remote user called 'webaccess': `mysql> CREATE USER 'webaccess'@'%' IDENTIFIED WITH mysql_native_password by 'PassWord.1';`

* Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr: `mysql> GRANT ALL PRIVILEGES ON tooling.* TO 'webaccess'@'172.132.8.4/20';`

* Flushing the privileges so that MySQL will begin to use them: `mysql> FLUSH PRIVILEGES;`

* Checking the user and host assigned to: `mysql> select user, host from mysql.users`

* Exiting from MySQL shell: `mysql> exit;`

![ec2](./img/2-lvm.PNG)

## STEP 3: PREPARE THE WEB SERVERS

We need to make sure that our Web Servers can serve the same content from shared storage solutions, in our case – NFS Server and MySQL database.

During the next steps we will do following:

* Configure NFS client (this step must be done on all three servers)
* Deploy a Tooling application to our Web Servers into a shared NFS folder
* Configure the Web Servers to work with a single MySQL database


1- Launch a new EC2 instance with RHEL 8 Operating System

![ec2](./img/1-ec2.PNG)


2- Install NFS client: `sudo yum install nfs-utils nfs4-acl-tools -y`

![ec2](./img/2-lvm.PNG)

3- Mount /var/www/ and target the NFS server’s export for apps:

```
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www 

```

4- Verify that NFS was mounted successfully by running `df -h`.
* Make sure that the changes will persist on Web Server after reboot:
`sudo vi /etc/fstab`

add following line

`<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0`

e.g `172.123.8.1:/mnt/apps /var/www/ nfs defaults 0 0`

![ec2](./img/2-lvm.PNG)

5- Install Remi’s repository, Apache and PHP

```
sudo yum install httpd -y

sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo dnf module reset php

sudo dnf module enable php:remi-7.4

sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

sudo systemctl start php-fpm

sudo systemctl enable php-fpm

setsebool -P httpd_execmem 1

```

**Repeat steps 1-5 for another 2 Web Servers.**

6 - Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps

You can try to create a new file touch test.txt from one server


7 - Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs
`sudo mount -t nfs -o rw,nosuid 172.31.17.5:/mnt/logs /var/log/httpd`

* Repeat step №4 to make sure the mount point will persist after reboot.

`sudo vi /etc/fstab`
`<NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd nfs defaults 0 0`
e.g `172.123.8.1:/mnt/apps /var/log/httpd nfs defaults 0 0`

![ec2](./img/2-lvm.PNG)


8 - Fork the tooling source code from Darey.io Github Account to your Github account.

* Install git on the webserver: `sudo yum install git`

* Initialize a git entry repository: `git init`

* Go to Darey.io Github account, click on code and copy the HTTPS url from the Github repository.

* Next clone the git url on our terminal with this command: 
`git clone https://github.com/darey-io/tooling.git`
 


![ec2](./img/2-lvm.PNG)


 9 - Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

 * Copy the content of html directory inside the tooling directory into the /var/www/html with this command: `sudo cp -R html/. /var/www/html/`

* We can now confirm that the files are copied to the right location: `ls /var/www/html`

![ec2](./img/2-lvm.PNG)

* Note: To allow http connection into the webserver, we opened Port 80 on our webserver inside the EC2 instance 

![ec2](./img/1-ec2.PNG)

Note 2: When we encountered 403 Error – we checked permissions to our /var/www/html folder and also `disable` SELinux `sudo setenforce 0`



10 - Update the website’s configuration to connect to the database (in `/var/www/html/functions.php` file). Apply tooling-db.sql script to your database using this command: `mysql -h <databse-private-ip> -u <db-username> -p <db-pasword> < tooling-db.sql`


e.g `mysql -h 172.132.2.1 -u webaccess -p PassWord.1 < tooling-db.sql`


**Hola, Done!**