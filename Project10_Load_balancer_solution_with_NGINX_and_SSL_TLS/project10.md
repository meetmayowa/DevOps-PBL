
# LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

## BACKGROUND
Task
This project consists of two parts:

* Configure Nginx as a Load Balancer as a spin off of Project 8
* Register a new domain name and configure secured connection using SSL/TLS certificates

## STEP 0: Launching A New Instance

I terminated the apache load balancer server and launched a new EC2 Instance(Ubuntu 20.04) which will be used as Nginx load balancer and named it "Nginx LB". I also started all the EC2 instances that was setup in project 7. Then I connected to the Ubuntu server on my terminal via ssh connection.


![ec2](./img/1-ec2.PNG)

* Opening TCP port 80 for HTTP and 443 for HTTPS on the security group

![ec2](./img/1-ec2.PNG)
## STEP 1: Configuring Nginx As A Load Balancer

* Updating the server:  `sudo apt update`

![update](./img/2-LVM.PNG)

* Upgrading the server: `sudo apt upgrade`

![upgrade](./img/2-LVM.PNG)

* Installing Nginx: `sudo apt install nginx`

![upgrade](./img/2-LVM.PNG)


* 2 - Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses: `sudo vi /etc/hosts`

![upgrade](./img/2-LVM.PNG)

* Configuring the Nginx LB to make use of the Webserver’s names defined in the /etc/hosts: `sudo vi /etc/nginx/nginx.conf`

![upgrade](./img/2-LVM.PNG)

* Adding the following configuration in the http section:

```
upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

```

![upgrade](./img/2-LVM.PNG)


* Restart Nginx service: `sudo systemctl restart nginx`

* And make sure the service is up and running: `sudo systemctl status nginx`


## STEP 2: REGISTER A NEW DOMAIN NAME AND CONFIGURE SECURED CONNECTION USING SSL/TLS CERTIFICATES.

* 1 - In order to get a valid SSL certificate – I registered a new domain name. 

![ec2](./img/1-ec2.PNG)

* 2 - I assigned an Elastic IP to my Nginx LB server and associated my domain name with this Elastic IP (Since I am using a free-tier EC2 services, I used the Public domain)

![ec2](./img/1-ec2.PNG)

* 3 - Update a **record** in my registrar to point to Nginx LB using Elastic IP address

![ec2](./img/1-ec2.PNG)


* Check that my Web Servers can be reached from my browser using new domain name using HTTP protocol – http://<your-domain-name.com>

![ec2](./img/1-ec2.PNG)


* 4 - Configure Nginx to recognize my new domain name: `sudo vi /etc/nginx/nginx.conf`

Update your `nginx.conf` with `server_name www.<your-domain-name.com>` instead of `server_name www.domain.com`

![upgrade](./img/2-LVM.PNG)

* 5 - Install certbot and request for an SSL/TLS certificate

* Make sure snapd service is active and running, inorder to install certbot: `sudo systemctl status snapd`

![upgrade](./img/2-LVM.PNG)

* Installing certbot that will be used to request for SSL/TLS certificate: `sudo snap install - -classic certbot`

![upgrade](./img/2-LVM.PNG)

* Creating a symlink for the certbot from /snap/bin to /usr/bin: `sudo ln -s /snap/bin/certbot /usr/bin/certbot`


![upgrade](./img/2-LVM.PNG)

* Requesting for my certificate and selecting my domain name: `sudo certbot - -nginx`

![ec2](./img/1-ec2.PNG)

* 5 - Set up periodical renewal of your SSL/TLS certificate

* Testing the renewal command in a dry-run mode: `sudo certbot renew - -dry-run`


![upgrade](./img/2-LVM.PNG)


* Configuring a cronjob to run renew command for my SSL/TSL certificate twice a day: `crontab -e`


![upgrade](./img/2-LVM.PNG)


* Adding the following line:
`* */12 * * * root /usr/bin/certbot renew > /dev/null 2>&1`



![upgrade](./img/2-LVM.PNG)