
1- In this project, I am to onboard 20 new Linux users onto a server. 

2- Create a shell script that reads a csv file that contains the first name of the users to be onboarded.

3- The script I created should read the CSV file, create each user on the server, and add to an existing group called developers (I created this group ahead).

4- Ensure that your script will first check for the existence of the user on the system, before it will attempt to create that it.

5- Ensure that the user that is being created also has a default home folder

6- Ensure that each user has a .ssh folder within its HOME folder. If it does not exist, then create it.

7- For each user’s SSH configuration, I create an authorized_keys file and add ensure it has the public key of your current user.

## STEP 1: Preparing Prerequisites
To setup a virtual server, I Created a new EC2 Instance of t2.nano family with Ubuntu Server 20.04 LTS (HVM) image from aws account which is the free tier(limited) offered by aws.

 After a successful launch of the EC2 instance(ubuntu server), I connected to the EC2 instance from as a window user terminal with my private key(.pem file).


 ![es2](./images/59-ec2.PNG)
 
 ## STEP 2: Copy the onboard.sh that was created into the instance
I used the SSH command to copy the Onbaord.sh into the instance using the public IP to connect 

 `scp -i meetmayowa-ec2.pem onboard.sh ubuntu@3.91.189.78:~/;`

 ![onboarding](./images/59-ec2.PNG)



## STEP 3: Preparing the Server, creating the directory and files.

* Create the project Directory called Shell: 
`mkdir Shell`

* Move the onboard.sh file in the Shell directory: `mv onboard.sh /home/ubuntu/Shell`

* Move into the Shell folder: `cd Shell`

* Create a csv file name names.csv: `touch names.csv`

* Open the names.csv file: `vim names.csv`

* Insert some random names into it. (One name per line)


   ![names](./images/60-names.PNG)

* Create a csv file name names.csv: `touch id_rsa.pub`

* Open the names.csv file: `vim id_rsa.pub`

```
AAAAB3NzaC1yc2EAAAADAQABAAABgQCzKZyicHxIkklSrNlxsJyyTrcIdBIt84Z0cQb3R4k0jH53kxkaT5hP8tfWTe62LXi7vV86fY+SX7TBNM76XGCbw/6vrMGegm6J1x2i1AiLNwq5nqTjOGn0AIwku4IlCCLAB7tdfRyVuCarmBlwny3lzRyybIUAWXR/D6vpN09MsDILbKdhay+Q/p9OUBMSLPqXdY/QIh/Oe3rVv1lwY3AohNfq7V3tO88zKswfA5iiexNiSYX1myT0OrX8cBE771j9quoNZhQgaLI1mIMtAvnHQChrn9k2nUaO/BMBCQGol5XzGv1ado7hgoVPoluIUD+FGNo/pH4zcmDLICH6drXY/C9MESnkMUPLFxBXKO/OitApY71vRao9nAhAwpVMsy6FqiOb5uawhvhoHYIHTV/f4EtagVagRMP2PxYMYR6jykIV4MPJTkCm+lGhTyMlRu+qRQjdLn8AAtHf4aEV8dIkoGh088DI7eA/4o0wz4OV4upH5ewSFS+5IHmRECEW5Nc=
```

* Create a csv file name names.csv: `touch id_rsa`

* Open the names.csv file:  `vim id_rsa`

```
-----BEGIN OPENSSH PRIVATE KEY-----
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
kmaSGj362OnCCNAAAACWRhcmVARGFyZQE=
-----END OPENSSH PRIVATE KEY-----
```

* Update the file path where the id_rsa.pub file that is located in the onboard.sh file so that we can properly store the authorized_key
 when created. 

  ![update](./images/61-update.PNG)


* We created group called 'developers': 
 `sudo groupadd develops`

* Before we run onboard.sh, we need to make it an executable file : `sudo chmod +x onboard.sh`

* Switch to Super user by using this command: `sudo su`

* Now that you are now in the root inside the Shell directory as a Super User, run ./onboard.sh
`./onboard.sh`

* By now all the users have been create.    

* To view the users that were created, you this command : `ls /home/`

  ![newuser](./images/62-new-users.PNG)


## STEP 4: Testing Users to make sure they can connect to the server

* Test a few of the users randomly, and ensure that you are able to connect to the server using the private key and the public key.


* Open a new window and create onboard.pem file in the Downloads directory, or depending on the directory you perfer: `vi onboard.pem`

* Copy and paste the private key that was shared with us and paste it inside the onboard.pem file. Save and close the file. 

* We need to check if the onboard.pem is write protected with this command: `ls -l | grep onboard.pem` 

* Protect the onboard.pem with this command: `sudo chmod 600 onboard.pem`


* Next to connect as a New user called "Obi" using the SSH command line. 
Firstly go back to the instance and copy the Public copy and run this command: `ssh -i onboard.pem Obi@3.91.189.78`

    (i.e. `ssh -i pem.key user@public-ip-address`)



  ![connected](./images/63-user-connected.PNG)

* To make sure that the user does not have the sudoer access to the server, run this command to confirm: `sudo apt update`

  ![sudoer](./images/64-sudoer.PNG)


* To confirm that the SSH file has been created: `ls -la .ssh/`

  ![ssh](./images/65-ssh.PNG)


* You can cat to see the details of authorized key and there you will see the public key:  `cat .ssh/authorized_keys`

  ![ssh](./images/66-authorized.PNG)