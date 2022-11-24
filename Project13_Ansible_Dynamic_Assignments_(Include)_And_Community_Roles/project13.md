# INTRODUCING ANSIBLE DYNAMIC ASSIGNMENTS(INCLUDE) AND COMMUNITY ROLES

## BACKGROUND

In continuation with [Project 12](https://github.com/meetmayowa/DevOps-PBL/blob/main/Project12_Ansible_Refactoring_And_Static_Assignement_(Imports_And_Roles)/project12.md), dynamic assignment is introduced by making use of include modules. By dynamic, it means that all statements are processed only during execution of the playbook which is the opposite of the import modules.

The following steps outlines how include module is used for running dynamic environment variable:

## STEP 1: Introducing Dynamic Assignment Into The Project

* Checking out to a new branch in the same ansible-config-mgt repository and naming it ‘dynamic-assignments’

![plugins](./img/2-plugins.PNG)

* Creating a new folder in the root directory of the repository and naming it ‘dynamic-assignments’

![plugins](./img/2-plugins.PNG)

* Creating an environment variable file in the dynamic-assignments directory and naming it ‘env_vars.yml’

![plugins](./img/2-plugins.PNG)

* Creating a folder that holds the environmental variable and naming it ‘env-var’
* Creating the following files under it: dev.yml, uat.yml, prod.yml and stage.yml
* The structure of the ansible-config-mgt folder will be as displayed below:

![plugins](./img/2-plugins.PNG)

* Entering the following codes in the env_vars.yml file:

```
---
- name: collate variables from env specific file, if it exists
  hosts: all
  tasks:
    - name: looping through list of available files
      include_vars: "{{ item }}"
      with_first_found:
        - files:
            - dev.yml
            - stage.yml
            - prod.yml
            - uat.yml
          paths:
            - "{{ playbook_dir }}/../env-vars"
      tags:
        - always

```

![plugins](./img/2-plugins.PNG)

* Updating site.yml file to work with dynamic-assignments:

![plugins](./img/2-plugins.PNG)

## STEP 2: Implementing Community Roles

In order to preserve my github state whenever I install a new role in the ansible-config-mgt project on the the bastion server, I made use of git commands so I can easily commit the changes made and pushing it to the ansible-config-mgt repository directly from the bastion server

* Installing git packages: `sudo apt install git`
* Initializing the ansible-artifact-config directory: `git init`


![plugins](./img/2-plugins.PNG)

* Pulling the ansible-config-mgt repository: `git pull https://github.com/somex6/ansible-config-mgt.git`

![plugins](./img/2-plugins.PNG)


* Registering the repo: `git remote add origin https://github.com/meetmayowa/ansible-config-mgt.git`
* Creating a new branch 'roles-feature': `git branch roles-feature`
* Switching to the new branch: `git switch roles-feature`


![plugins](./img/2-plugins.PNG)

* Making use of community roles by installing a MySQL role already configured from ansible-galaxy by geerlingguy in the role directory: $ ansible-galaxy install geerlingguy.mysql

![plugins](./img/2-plugins.PNG)

* Renaming the role folder to mysql: `mv geerlingguy.mysql/ mysql`

![plugins](./img/2-plugins.PNG)

* Updating the ansible-config-mgt repository

**git add .**

![plugins](./img/2-plugins.PNG)

**git commit -m "Commit new role files into GitHub"**

![plugins](./img/2-plugins.PNG)

**git push --set-upstream origin roles-feature**

![plugins](./img/2-plugins.PNG)

* Creating a pull request

![plugins](./img/2-plugins.PNG)

* Merging the request

![plugins](./img/2-plugins.PNG)

## STEP 3: Implementing Load Balancer(Apache & Nginx) Roles
Two load balancer roles are setup which are Nginx and Apache roles, but because a web server can only make use of one load balancer, the playbook is configured with the use of conditionals- when statement, to ensure that only the desired load balancer role tasks gets to run on the webserver

* Setting up apache role in the role directory: `sudo ansible-galaxy init apache`
The folder structure of Apache role:

![plugins](./img/2-plugins.PNG)

* Setting up nginx role in the role directory: `sudo ansible-galaxy init nginx`


**The folder structure of Nginx role:**

![plugins](./img/2-plugins.PNG)


* Entering the following code task in apache/tasks/main.py file:

```
---
- name: install apache
  become: true
  apt:
    name: apache2
    state: present

- name: Start service apache, if not started
  become: true
  service:
    name: apache2
    state: started

```

![plugins](./img/2-plugins.PNG)

* Entering the following code in nginx/tasks/main.py file:

```
- name: install ngnix
  become: true
  apt:
    name: nginx
    state: present

- name: Start nginx service, if not started
  become: true
  service:
    name: nginx
    state: started

```


![plugins](./img/2-plugins.PNG)

* Declaring the following variable in the 'defaults/main.py' file of both apache and nginx roles file which makes ansible to skip the roles during execution.


**For apache/defaults/main.yml**

```
---
enable_apache_lb: false
load_balancer_is_required: false

```


![plugins](./img/2-plugins.PNG)


**For nginx/defaults/main.yml**

```
---
enable_apache_lb: false
load_balancer_is_required: false

```


![plugins](./img/2-plugins.PNG)

* Creating a file in the static-assignment folder and naming it ‘loadbalancers.yml’ and entering the following codes:

```
- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }

```

![plugins](./img/2-plugins.PNG)

* Updating the site.yml file:

```
---
- name: Loadbalancers assignment
  hosts: lb
- import_playbook: ../static-assignments/loadbalancers.yml
  when: load_balancer_is_required 

```

![plugins](./img/2-plugins.PNG)

* To define which load balancer to use, the files in the env-var folder is used to override the default settings of any of the load balancer roles. In this case the env-var/dev.yml file is used to make ansible to only run nginx load balancer task in the target server:

**env-var/dev.yml file**

```
enable_nginx_lb: true
load_balancer_is_required: true

```
![plugins](./img/2-plugins.PNG)

* Running the playbook: `sudo ansible-playbook -i /home/ubuntu/ansible-config-artifact/inventory/dev.yml /home/ubuntu/ansible-config-artifact/playbooks/site.yml`

![plugins](./img/2-plugins.PNG)