# ANSIBLE REFACTORING AND STATIC ASSIGNMENT (IMPORTS AND ROLES)

## INTRODUCTION
In continuation of [Project 11](https://github.com/meetmayowa/DevOps-PBL/blob/main/Project11_Ansible_Configuration_Management_To_Automate_Several_Projects/project11.md), the ansible code in my ansible-config-mgt repository is refactored into making use of the import functionality– which allows us to effectively re-use previously created playbooks in a new playbook, and assigning task in the playbook with role functionality.

The following outlines the steps taken:

## STEP 1: Enhancing The Jenkins Job

* Since artifacts stored in Jenkins server changes directory at each build. Inorder to keep tabs on recent artifacts, copy-artifacts plugin is implemented to copy recent artifacts from a build and paste it automatically to a specified directory where ansible playbook can be run with ease.
* Creating a new directory on the Jenkins-ansible server where the artifacts will be copied to: `sudo mkdir /home/ubuntu/ansible-config-artifact`
* Changing the permissions: `chmod -R 0777 /home/ubuntu/ansible-config-artifact`