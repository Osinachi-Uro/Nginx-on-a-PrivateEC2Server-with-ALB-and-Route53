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
> * Create security group with inbound rule to allow ssh traffic from Anywhere 0.0.0.0/0
> 
> Launch Instance.
<img width="786" alt="Bastion host summary" src="https://user-images.githubusercontent.com/83463641/211542294-fd0c2438-05f8-417c-98c0-dd81f838e84b.PNG">


#### 3. Create 2 private EC2 Instances
Create each of the two private EC2 instances separately following the same steps as the Bastion host with the following important changes:
> * Place each private EC2 instance in one of the 2 private subnets.
> * Disable auto-assign public IP
> * Select the same security group created in step 2 for the bastion host

**Instance Summary for private EC2 1 and 2**

<img width="783" alt="ec21" src="https://user-images.githubusercontent.com/83463641/211544517-2cb56ce2-fca8-4c03-b975-61ed2dc408a8.PNG">

<img width="770" alt="ec22" src="https://user-images.githubusercontent.com/83463641/211544556-874a0e98-4d7b-4269-a70f-5df0e554b775.PNG">



