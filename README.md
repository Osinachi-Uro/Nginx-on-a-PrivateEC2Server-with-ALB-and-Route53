# AltSchool-Holiday-Challenge

This was a 2022 Christmas Holiday Challenge for AltSchool Africa.

# Task

Set up 2 EC2 instances on AWS (use the free tier instances). Deploy an Nginx web server on these instances (you are free to use Ansible) 
Set up an ALB(Application Load balancer) to route requests to your EC2 instances.
Using any programming language of your choice make sure that each server displays its own Hostname or IP address.  

## Note
* Your web servers should not be accessible through their respective IP addresses access must be only via the load balancer. 
* You should define a logical network on the cloud for your servers. 
* Your EC2 instances must be launched in a private network. 
* Your Instances should not be assigned public IP addresses. 
* You may or may not set up auto scaling.
* You must submit a custom domain name (from a domain provider e.g. Route53) or the ALB’s domain name.
