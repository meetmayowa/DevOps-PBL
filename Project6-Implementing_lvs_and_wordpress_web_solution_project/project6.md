# IMPLEMENTING LOGICAL VOLUME MANAGEMENT AND WORDPRESS WEB SOLUTION PROJECT

## BACKGROUND:

In this project, storage infrastructure is prepared to implement a Three-tier Architecture(in this case - a laptop to serve as a client, an EC2 linux server as a web server and an EC2 Linux server as a database server) of which the web solution used is wordpress and the disks used to store files are adequately partitioned and managed through programs such as gdisk and LVM respectively.

My 3-Tier Setup
1- A Laptop or PC to serve as a client
2- An EC2 Linux Server as a web server (This is where I installed WordPress)
3- An EC2 Linux server as a database (DB) server
Use RedHat OS for this project

The following are the steps I took to implementing LVM and basic web solution-wordpress:


## STEP 1: SETTING UP AND ATTACHING EBS VOLUMES TO AN EC2 SERVER FOR THE “WEB SERVER”

* Launching a two EC2 instance(Red Hat Enterprise Linux 8 HVM) that will serve as a “web server” and the "database-server" respectively. Note that we are going to configure the websever first. 

![ec2](./img/1-ec2.PNG)

* Creating 3 EBS volumes in the same Availability Zone (us-east-1c) with my EC2 instance created. 

![volumes](./img/2-volumes.PNG)

* Attach a volume to an instance to use it as you would a regular physical hard disk drive.

![attachment](./img/3-attach.PNG)

## STEP 2: PARTITIONING THE VOLUMES ATTACHED TO THE EC2 INSTANCE AND CREATING LOGICAL VOLUME WITH IT.

Open the Linux terminal to start the configuration and partitioning. 

Partitioning the volumes attached to the Ec2 Instance and creating Logical volume with It

After a successful SSH connection to the EC2 instance on my terminal, running the following commands:

* To inspect what block devices are attached to the server: $ lsblk
* To see all mounts and free space: $ df –h

![partition](./img/4-partition.PNG)

Creating a single partition on each of the 3 disks:

* For xdvf disk:  `sudo gdisk /dev/xvdf`
* Entering `‘n’` key to add a new partition
* Accepting all the defaults settings and selecting Linux LVM type of partition by entering `8e00` code and hit enter
* Entering the `‘w’` key to write the new partition created and confirming with the `‘Y’` and hit enter.

![part1](./img/5-part1.PNG)

* I followed the same process to create single partition for the xvdg and xvdh drives respectively

* Use `lsblk` utility to view the newly configured partition on each of the 3 disks.

![new-part](./img/6-new-part.PNG)

* Installing LVM2 package for creating logical volumes on a linux server using this command: `sudo yum install lvm2`

![lvm](./img/7-lvm2.PNG)

Note: Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.

* Checking for available partitions:  `sudo lvmdiskscan`

![diskscan](./img/8-diskscan.PNG)
 

* Use `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM: `sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1` 

![pvcreate](./img/9-pvcreate.PNG)

* Check if all the physical voulumes have been created properly 

![pvs](./img/10-pvs.PNG)


* Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg: `sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

* Verify that my VG has been created successfully by running `sudo vgs`

![vgs](./img/11-vgs.PNG)

* On the Volume group, we can now create our Logical Volume (app-lv and logs-lv)

Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

![lvs](./img/12-lvs.PNG)

* To display the entire setup, use this command: `sudo vgdisplay -v`

![display](./img/13-display.PNG)

* And also use this command: `sudo lsblk` to display the entire setup

![lsblk](./img/14-lsblk.PNG)

 ## STEP 3: FORMATTING AND MOUNTING THE LOGICAL VOLUMES

We are going to make the 2 Logical Volume a filesystem 
* Formatting the 2 logical volumes with ext4 filesystem:

    `sudo mkfs –t ext4 /dev/webdata-vg/apps-lv`

    `sudo mkfs –t ext4 /dev/webdata-vg/logs-lv`

![ext4](./img/15-ext4.PNG)

* Next is to create a mount point for our devices
* Creating a directory where the website files will be stored: `sudo mkdir –p /var/www/html`
* Create /home/recovery/logs to store backup of log data: `sudo mkdir -p /home/recovery/logs`
* Mounting apps-lv logical volume on /var/www/html: `sudo mount /dev/webdata-vg/apps-lv /var/www/html`

* Check the log to make sure it is empty, if it is not then you will need to back it up. Use this command to check: `sudo ls -l /var/log`

![log](./img/17-log.PNG)
* Using rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs: `sudo rsync –av /var/log/. /home/recovery/logs/`

![copied](./img/18-copied.PNG)

*Use this command line to confirm that it has been backed up: `sudo ls -l /home/recovery/logs/log`
* We can go ahead and mount on var/log directory: `sudo mount /dev/webdata-vg/logs-lv /var/log`
* Its very important we restore back the files that we backup earlier into var/log directory by using the following command: `sudo rsync -av /home/recovery/logs/. /var/log`

![recover](./img/19-recover.PNG)


* Locate the UUID of the device that will be used to update the /etc/fstab file and copy it;

    `sudo blkid`

![blkid](./img/20-blkid.PNG)

* Updating the fstab file so that mount configuration will persist after restart of the server: 

   `sudo vi /etc/fstab`

![edit](./img/21-edit.PNG)


* Test the configuration:  `sudo mount -a`

* Reload the daemon: `sudo systemctl daemon-reload`
 

* Verify your setup by running `df -h`, output must look like this:

![reload](./img/22-reload.PNG)

## STEP 4: PREPARING THE DATABASE SERVER


## STEP 5: INSTALLING WORDPRESS ON THE WEB SERVER EC2 INSTANCE


## STEP 6: INSTALLING MYSQL ON THE DATABASE SERVER EC2 INSTANCE


## STEP 7: CONFIGURING DB TO WORK WITH WORDPRESS