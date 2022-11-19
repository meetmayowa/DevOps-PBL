# ANSIBLE CONFIGURATION MANAGEMENT TO AUTOMATE SEVERAL PROJECTS

## INTRODUCTION
In this project, the Jenkins server from [Project 9](https://github.com/meetmayowa/DevOps-PBL/blob/main/Project9_Working_with_Jenkins_CICD_project/project9.md) Web Architecture is setup and configured to function as a Jump Server or Bastion Host by using Ansible Configuration Management which helps to automate most of repetitive tasks done in previous projects.


This Project will make you appreciate DevOps tools even more by making most of the routine tasks automated with Ansible Configuration Management, at the same time you will become confident at writing code using declarative language such as YAML.


## Task
* Install and configure Ansible client to act as a Jump Server/Bastion Host
* Create a simple Ansible playbook to automate servers configuration


The following outlines the steps I took in configuring an Ansible server and also configuring a build task in Jenkins job that is linked to my Github repository to trigger whenever there is a change in the main branch by the use of webhooks, of which the artifacts from the build are used to automate tasks with Ansible playbook.


![architecture](./img/1-archi.png)



## STEP 1: Connecting To The Jenkins Server


* 1 - I updated the tag name of the `Jenkins server` to `Jenkins-Ansible` and connecting to it from my terminal via ssh connection and also starting the whole servers setup in project 7-8 for ansible playbook to perform task on them.


![ec2](./img/1-ec2.PNG)

* 2 - In your GitHub account create a new repository and name it `ansible-config-mgt`


![ansible-repo](./img/2-ansible-repo.PNG)



* 3 - Configuring Ansible In The Jenkins-Ansible Server

* Update the server `sudo apt update`

* Install Ansible on the server `sudo apt install ansible`


![update](./img/3-update.PNG)

* Check my Ansible version by running:  `ansible --version`

![version](./img/4-version.PNG)


* 4 - Configure Jenkins build job to save my repository content every time I change it.

## STEP 2: Creating a Freestyle Job

* i-  I created a new Freestyle project `ansible` in Jenkins and point it to your `ansible-config-mgt` repository.

* Opening the Jenkins web console from my web browser with the Jenkins server IP address and creating a freestyle job called ansible


![freestyle](./img/5-freestyle.PNG)

* Grab the url from the Github repo and paste it into the Jenkins configuration page 

![url](./img/6-url.PNG)

* Firstly, I clicked on the general configuration and clicked on "Git" button under the source code management

* To connect my GitHub repository, I provided its URL, I copied it from the repository itself.



* Configure Webhook in GitHub and set webhook to trigger ansible build.

 Enable webhooks in your GitHub repository settings


![webhook](./img/7-webhook.PNG)


![webhook2](./img/8-webhook2.PNG)


* Inputting the ansible-config-mgt repository url and adding my credentials

* To make sure that the build runs automatically whenever a change is made on the Git repository via the webhooks, I proceeded to click on "configure"

* In configuration of my Jenkins freestyle project, I choose Git repository, provided there the link to my Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![credential](./img/10-credential.PNG)


* On the Build Triggers section, selecting GitHub hook trigger for GITScm polling



![trigger](./img/11-trigger.PNG)



* And on the Post-build Actions, clicking on Add post-build action and selecting Archive the artifacts to archive all the files resulted from the build


![archive](./img/12-archive.PNG)

* Clicking on “Add post-build action” and selecting “Archive the artifacts” to archive all the files(artifacts) resulted from the build and hit save

![artifacts](./img/13-artifacts.PNG)

* Change the Branches to build from */master to */main

![main](./img/14-branches.PNG)


* Making a change in the README.md file in my repository and pushing it the main branch to test the configuration. Going back to the Jenkins web console to confirm that ansible build is triggered.

![update](./img/15-readme.PNG)


* A new build has been launched automatically (by webhook) and I could see its results – artifacts, saved on Jenkins server.

![update](./img/16-console.PNG)


* Verifying the location of the stored artifacts: `ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/`

![build](./img/17-build.PNG)

## Step 3: Prepare my development environment using Visual Studio Code

Working With Visual Studio Code Application
A VS code application is setup that will help in better coding experience and debugging and for pushing and pulling codes easily from Github.

* I connected to the ansible-config-mgt repository from VSCode application
* Creating a new branch from the ansible-config-mgt repository called feature on the VS Code terminal that will be used for development of a new feature.

![clone](./img/18-git-clone.PNG)


`git Checkout -b feature`

![feature](./img/19-feature.PNG)


## STEP 4: Implementing SSH-Agent On Jenkins-Ansible Server

* Inputting the following code:

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'

```


![dev](./img/31-dev.PNG)



## STEP 5: Create a Common Playbook

* In `common.yml` playbook I wrpte configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

* I Updated my playbooks/common.yml file with following code:

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest

```



![dev](./img/31-dev.PNG)



## Step 6: Update GIT with the latest code

* Committing the code from the feature branch 
`git status` and `git add` command

![status](./img/34-git-status.PNG)

* Git Commit: `git commit -m "Updating "`


![commit](./img/35-git-commit.PNG)


* Pushing the code which will create a push request `git push upstream`

![push](./img/36-git-push.PNG)


* Pull request created



![pull](./img/38-pull-request.PNG)


* Verify the push was success by checking out the codes


![code](./img/39-code.PNG)



* Merging the code to the main branch


![merge](./img/37-confirm-merge.PNG)


* On the Jenkins web console, ansible build is also triggered


![build](./img/40-ansible-build.PNG)




![console](./img/41-console.PNG)



* Heading back to my VSCode terminal to checkout to the main branch: `git checkout main`


![console](./img/44-git-checkout.PNG)


* pulling down the changes: `git pull`


![console](./img/45-git-pull.PNG)


## STEP 7: Running Ansible Command


* Executing the ansible-playbook command: `ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/playbooks/common.yml`

e.g Check the build number and replace it respectively


`ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/5/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/5/archive/playbooks/common.yml`


![ssh](./img/47-ssh1.PNG)

![ssh](./img/47-ssh.PNG)


NOTE: Update my ansible playbook with some new Ansible tasks and went through the full checkout -> change codes -> commit -> PR -> merge -> build -> ansible-playbook cycle again to see how easily I can manage a servers fleet of any size with just one command!