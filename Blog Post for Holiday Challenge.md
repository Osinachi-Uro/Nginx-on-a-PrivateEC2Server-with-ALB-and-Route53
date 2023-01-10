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
3. Create 2 private EC2 instatnces
4. Connect to the privite instances through the Bastion Host
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
> Create new key pair: Type RSA; Format .pem.
> Edit Network settings:
> * VPC: Same VPC created in step 1
> * Subnet: Select one of the public subnets created earlier.
> * Enable auto-assign public IP
> * Create security group with inbound rule to allow ssh traffic from Anywhere

