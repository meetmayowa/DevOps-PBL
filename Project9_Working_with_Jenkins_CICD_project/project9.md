# TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS - CI/CD PROJECT

## BACKGROUND
In this project we are going to start automating part of our routine tasks with a free and open source automation server – Jenkins. It is one of the mostl popular CI/CD tools. 

 In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub https://github.com/meetmayowa/tooling will be automatically be updated to the Tooling Website.

**TASK**

Enhance the architecture prepared in [Project 8](https://github.com/meetmayowa/DevOps-PBL/blob/main/Project8_Load_balancer_solution_with_apache/project8.md) by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.


## INSTALL AND CONFIGURE JENKINS SERVER


## STEP 1: Preparing Prerequisites
To setup a virtual server, I Created a new EC2 Instance of t2.nano family with Ubuntu Server 20.04 LTS (HVM) that will be used as Jenkins Server and name it "Jenkins".

 After a successful launch of the EC2 instance(ubuntu server), I connected to the EC2 instance from as a window user terminal with my private key(.pem file).

![ec2](./img/1-ec2.PNG)

## STEP 2: Installing And Configuring The Jenkins Server

* Update the server: `sudo apt update`


![update](./img/2-update.PNG)


* Upgrade the server: `sudo apt upgrade`

![upgrade](./img/3-upgrade.PNG)

* 3 - Installing the Java Development Kit(JDK): `sudo apt install default-jdk-headless`

![headless](./img/3-headless.PNG)


* 3- Install Jenkins  

* Acquiring the Jenkin's key: `wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -`


* Adding to the source file the path to Jenkins installation: `sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'`


* Updating the server again: `sudo apt update`

![key](./img/4-key.PNG)



* Installing Jenkins: `sudo apt-get install jenkins`


![jenkins](./img/5-jenkins.PNG)


* To make sure Jenkins is up and running:  `sudo systemctl status jenkins`


![systemctl](./img/6-systemctl.PNG)


* 4 - By default Jenkins server uses TCP port 8080 – So I opened it by creating a new Inbound Rule in my EC2 Security Group


![security](./img/7-security.PNG)




* 5 - To perform initial Jenkins setup. From my  browser access Jenkins-Server-Public-IP-Address-or-Public-DNS-Name from port 8080

`http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080`
 
   e.g. `http://44.204.55.136:8080`

![unlock](./img/8-unlock.PNG)

* I was prompted to provide a default admin password
 

* To retrieve the password from my server with this command: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

![password](./img/9-password.PNG)

* Then you will be asked which plugings to install – **choose suggested plugins**.

![plugins](./img/10-plugins.PNG)

* Installation ongoing: 


![install](./img/11-install.PNG)

* Once plugins installation is done – I created an admin user and I got my Jenkins server address.

![install](./img/14-user.PNG)

* Instance configuration page

![instance](./img/12-instance.PNG)

* Jenkins is now ready! 

![ready](./img/13-ready.PNG)



* Jenkins Dashboard

![dashboard](./img/15-dashboard.PNG)


**The installation is completed!**


## STEP 3: Configure Jenkins to retrieve source codes from GitHub using Webhooks


We are going to trigger an event by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server. 

This will done by going to the Github settings of the tooling repository and clicking on webhooks and inputing the Jenkins public Ip address as the payload URL and saving the settings:


* 1 - Enable webhooks in your GitHub repository settings

![webhook](./img/16-webhook.PNG)


![webhoo2k](./img/16-webhook2.PNG)

* 2 - Go to Jenkins web console, click "New Item" and create a "Freestyle project"

![freestyle](./img/17-freestyle.PNG)

* Firstly, I clicked on the general configuration and clicked on "Git" button under the source code management

* To connect my GitHub repository, I provided its URL, I copied it from the repository itself. 

![repo](./img/18-repo.PNG)


* In configuration of my Jenkins freestyle project, I choose Git repository, provided there the link to my Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![jenkins](./img/19-jenkins.PNG)


* I saved the configuration and ran the build. For now I did it manually.


* I Clicked "Build Now" button, and the build was successfull and I saw it under #1

![credential](./img/20-credential.PNG)

* I opened the build and check in "Console Output" and I saw it ran successfully.

![build](./img/21-build.PNG)

* 3 -  To make sure that the build runs automatically whenever a change is made on the Git repository via the webhooks, I proceeded to click on "configure"

* Click "Configure" my job/project and add these two configurations

* On the Build Triggers section, selecting GitHub hook trigger for GITScm polling


![build-trigger](./img/23-build-trigger.PNG)

* And on the Post-build Actions, clicking on Add post-build action and selecting Archive the artifacts to archive all the files resulted from the build

![archive](./img/24-archive.PNG)


![save](./img/25-save.PNG)


* Now, I went ahead and make some change README.MD file in my GitHub repository and push the changes to the master branch.

![readme](./img/26-readme.PNG)

* A new build has been launched automatically (by webhook) and I could see its results – artifacts, saved on Jenkins server.

![build](./img/27-build.PNG)

* I have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). 


* Now to locate the artifacts that are stored on Jenkins server locally: 

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`

e.g. ` ls /var/lib/jenkins/jobs/tooling_github/builds/3/archive/`

![buildno](./img/30-buildno.PNG)


# CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH

## Step 4: Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "Publish Over SSH".

* 1 - Install "Publish Over SSH" plugin.

> On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item. 

> On "Available" tab search for "Publish Over SSH" plugin and install it


![publish](./img/31-publish.PNG)


![plugins](./img/32-plugins.PNG)

* Configuring the publish over ssh on the Manage Jenkins > Configure system settings to connect to the NFS server and setting the remote directory path to the /mnt/opt. Using the NFS server Private IP as the Hostname:

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

1-Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)

2-Arbitrary name

3-Hostname – can be private IP address of your NFS server

4-Username – ec2-user (since NFS server is based on EC2 with RHEL 8)

5-Remote directory – /mnt/apps since our Web Servers use it as a mounting point to retrieve files from the NFS server

![ssh-server](./img/33-ssh-server.PNG)

* Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

* I I saved the configuration, open my Jenkins job/project configuration page and add another one "Post-build Action"

![over-ssh](./img/34-over-ssh.PNG)

* I Configured it to send all files produced by the build into our previously define remote directory. In our case we copied all files and directories – so we use **.

![sourcefile](./img/35-sourcefile.PNG)


* I saved this configuration and went ahead, change something in README.MD file in your GitHub Tooling repository.

* Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:
```
SSH: Transferred 25 file(s)
Finished: SUCCESS
```



![console](./img/36-console.PNG)


* To make sure that the files in /mnt/apps have been updated – connect via SSH/Putty to your NFS server and check README.MD file  

Confirming whether the artifacts are suucessfully sent to NFS server in the /mnt/opt directory:


`cat /mnt/apps/README.md`

![console](./img/37-nfs-server.PNG)


* I saw the changes I had previously made in my GitHub – the job works as expected.

* This shows that the Jenkins server is connected successfully to the NFS server
