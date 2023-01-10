## AltSchool Africa 2022 Christmas Holiday Challenge
How I set up Nginx server to run a php application on two private EC2 instances behind an Application load balancer

### Task Instruction
* Set up 2 EC2 instances on AWS(use the free tier instances.
* Deploy an Nginx web server on these instances
* Set up an ALB(Application Load balancer) to route requests to your EC2 instances
* Make sure that each server displays its own Hostname or IP address. You can use any programming language of your choice to display this.
* Optionally, set up Auto Scaling for the 2 instances.

### Solution Outline
1. Create a Virtual Private Cloud (VPC)
2. Create a Bastion Host
3. Create 2 private EC2 instances
4. Connect to the private instances through the Bastion Host
5. Install Nginx and configure hostname
6. Set up Application Load balancer for the servers
7. Set up Auto Scalling.


#### 1. Create a Virtual Private Cloud (VPC)
*A VPC is an isolated portion of the AWS Cloud populated by AWS objects, such as Amazon EC2 instances.*

On the AWS management console, from services, under VPC there are two option for creating VPCs. One is the **VPC Only** which creates only the VPC resource and the other is **VPC and More** which creates the VPC and other networking resources, I used the second option, VPC and more as I wanted to also provsion other resources with the following options and other options were left as default.
> VPC Name (HCproject-vpc) 
> 
> IPv4 CIDR block of 10.0.0.0/16
> 
> Default Tenency
> 
> Two Availability zones for high availability
> 
> Two public subnets and Two private subnets
> 
> 1 NAT gateway in one AZ. **NAT gateways enable resources in private subnets to reach the internet.**
> 
> Added an S3 VPC endpoint to reduce the cost of the NAT gateway.

<img width="867" alt="VPC" src="https://user-images.githubusercontent.com/83463641/211520701-86c18cee-0b9c-4e3f-b9ab-fafe8f09a792.PNG">

#### 2. Create a Bastion Host
*According to AWS, a bastion host is a server whose purpose is to provide access to a private network from an external network, such as the Internet. Because of its exposure to potential attack, a bastion host must minimize the chances of penetration.*

Next from the list of services, under EC2 select to **Launch Instances** using the following options.
> Name (HC-Bastion for EC2)
> 
> Amazon Machne Image: Ubuntu Server 22.o4 LTS (HVM), SSD Volume Type. Free tier eligible.
> 
> Instance Type: t2.micro
> 
> Create new key pair: Type RSA; Format .pem. This action downloads the created .pem file on the system for later use.
> Edit Network settings:
> * VPC: Same VPC created in step 1
> * Subnet: Select one of the public subnets created earlier.
> * Enable auto-assign public IP
> * Create security group with inbound rule to allow ssh traffic from Anywhere 0.0.0.0/0
> 
> Launch Instance.
<img width="786" alt="Bastion host summary" src="https://user-images.githubusercontent.com/83463641/211542294-fd0c2438-05f8-417c-98c0-dd81f838e84b.PNG">


#### 3. Create 2 private EC2 Instances
Create each of the two private EC2 instances separately following the same steps as the Bastion host with the following important changes:
> * Names: (HC-EC21) and (HC-EC22)
> * Place each private EC2 instance in one of the 2 private subnets.
> * Disable auto-assign public IP
> * Select the same security group created in step 2 for the bastion host

**Instance Summary for private EC2 1 and 2**

<img width="783" alt="ec21" src="https://user-images.githubusercontent.com/83463641/211544517-2cb56ce2-fca8-4c03-b975-61ed2dc408a8.PNG">

<img width="770" alt="ec22" src="https://user-images.githubusercontent.com/83463641/211544556-874a0e98-4d7b-4269-a70f-5df0e554b775.PNG">

#### 4. Connect to the private instances through the Bastion Host
To reach each of the private instances, we will have to go through the public instance which is the bastion host in this case.
Take the following steps:
> * Open any CLI of your choice and cd into the directory where the .pem keypair file was downloaded. I called my file **Holiday.pem**
> 
> * While inside the directory, assign a 400 file permission to the .pem file using ```chmod 400 Holiday.pem```
> 
> * Next, ssh into the bastion host using the command ```ssh -i "Holiday.pem" ubuntu@ec2-54-226-168-26.compute-1.amazonaws.com```
> 
> * Type **yes** on the next prompt when asked "Are you sure you want to continue connecting?"
<img width="460" alt="ssh into bastion host" src="https://user-images.githubusercontent.com/83463641/211575104-0bb3df4e-6bd4-4543-9dca-bed757d99739.PNG">

> * While still inside the bastion host, create a file containing the key pair, with the same name as the one you downloaded. ```sudo nano Holiday.pem``` .
> 
> * Then ssh into each of the private EC2 instances one after the other. Using ```ssh -i "Holiday.pem" ubuntu@10.0.134.203``` for my **HC-EC21** and ```ssh -i "Holiday.pem" ubuntu@10.0.148.140``` for **HC-EC22** .

<img width="486" alt="ssh in EC21" src="https://user-images.githubusercontent.com/83463641/211594694-782fe497-db0a-4db1-9018-a14f454bf33a.PNG">

> * To confirm that my private server can reach the internet through the public subnet, i ran the following command ```ping google.com```

<img width="590" alt="Confirm that my private subnet can reach the internet through the public subnet" src="https://user-images.githubusercontent.com/83463641/211601489-8d101d3d-68ec-4f9a-bdef-ecc060892bfe.PNG">

#### 5. Install Nginx and Configure hostname
*According to Nginx.com, NGINX is an open source software for web serving, reverse proxying, caching, load balancing, media streaming, and more. It started out as a web server designed for maximum performance and stability. In addition to its HTTP server capabilities, NGINX can also function as a proxy server for email (IMAP, POP3, and SMTP) and a reverse proxy and load balancer for HTTP, TCP, and UDP servers.*

To set up Nginx, which is part of the requirements of this assignment, I ran the following commands on each of the private servers one at a time:

> * ```sudo apt update```
> 
> * ```sudo apt install nginx -y```
> 
> * Change into a root user with ```sudo su``` and run the next command to replace the default nginx file with the server hostname
> 
> ```echo "<h1>Hello Valentine! This is my first server $(hostname -f)</h1>" > /var/www/html/index.nginx-debian.html```
> 
> * To confirm that the previous command was executed run the following command to check. This command will display the content of the file.
>   ```cat /var/www/html/index.nginx-debian.html```
>   
> * Exit the root user and enable Nginx to apply the changes ```sudo systemctl enable nginx```
> 
> * Start Nginx with ```sudo systemctl start nginx```
> 
> * Check Nginx status with this command ``` sudo systemctl status nginx``` to confirm its running.

<img width="840" alt="Nginx setup" src="https://user-images.githubusercontent.com/83463641/211667994-33c3a2be-5c14-42cc-bd39-2f082ff531ec.PNG">

> * Logout and repeat the same process for the second private EC2 Instance.
<img width="833" alt="server 2" src="https://user-images.githubusercontent.com/83463641/211672386-1ffd32f7-2437-4621-a721-70f19ab15522.PNG">

#### 6. Set up Application Load balancer for the servers
*Elastic Load Balancing (ELB) automatically distributes incoming application traffic across multiple targets and virtual appliances in one or more Availability Zones (AZs).

AWS has three different types of Elastic Load balancers which are Application Load balancer, Gateway Load balancer and Network Load balancer. For this exercise we will be setting up an Application Load Balancer fr our EC2 instances. From the EC2 page on the AWS management console, select Load balancers under **Load Balancing** then select **Create load balancer** take the following steps.
> * Select 'create' for Application Load Balancer.
> * Name: ()
> * Internet facing and IPv4
> * Select the same VPC we created for the project.
> * Map to the two private subnets in separate availability zones
> * Create a new security group called **HCALB sg** for the load balancer, and allow Inbound HTTP and HTTPS traffic on ports 80 and 443. Allow all outbound traffic.
> * For the Listeners, we choose to create a Target group:
>> * Target group type is Instances
>> * Target Group name (HCTargetGroup)
>> * Select the project VPC
>> * Protocol version is HTTP1
>> * Select the private instances and click the botton **Include as pending below** then create target group
> * Back to the ALB creation page and select the newly created target group, the create load balancer.
> 
