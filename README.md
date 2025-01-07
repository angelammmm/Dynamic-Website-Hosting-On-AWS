This guide provides detailed steps to build a three-tier Virtual Private Cloud (VPC) in Amazon Web Services (AWS) using a LAMP (Linux, Apache, MySQL, PHP) stack.

Prerequisites:

Understanding of basic networking concepts: DNS, route tables, subnets, and firewalls.
Familiarity with AWS services: VPC, EC2, RDS, and Application Load Balancer.
Architecture Overview:

The architecture comprises:

Presentation Tier: Public subnets hosting a bastion (jump) host for secure SSH access.
Application Tier: Private subnets hosting EC2 instances running the Apache web server and PHP.
Data Tier: Private subnets with RDS instances for the MySQL database.
Steps to Build the 3-Tier VPC:

Create a VPC:
Define a VPC with a CIDR block, e.g., 10.0.0.0/16.
Enable DNS hostnames for the VPC.
Set Up Subnets:
Create public subnets for the bastion host in multiple availability zones.
Create private subnets for application servers and databases in corresponding availability zones.
Configure Internet and NAT Gateways:
Attach an Internet Gateway to the VPC for public subnet access.
Set up a NAT Gateway in a public subnet to allow private subnets to access the internet securely.
Establish Route Tables:
Create and associate a public route table with the public subnets, routing traffic through the Internet Gateway.
Create private route tables for the application and database subnets, routing internet-bound traffic through the NAT Gateway.
Launch EC2 Instances:
Deploy a bastion host in the public subnet for SSH access.
Deploy application servers in the private subnets, ensuring security groups allow necessary traffic (e.g., HTTP from the Load Balancer, SSH from the bastion host).
Install LAMP Stack on Application Servers:
SSH into application servers via the bastion host.
Install Apache, PHP, and necessary PHP modules.
Configure Apache to serve your application.
Set Up RDS for the Database Layer:
Create a subnet group encompassing the private database subnets.
Launch an RDS instance within this subnet group, ensuring it's not publicly accessible.
Configure security groups to permit database connections from the application servers.
Configure Application Load Balancer (ALB):
Set up an ALB in the public subnets to distribute traffic to the application servers.
Ensure the ALB's security group allows inbound HTTP traffic.
Register the application servers with the ALB's target group.
Finalize Security Groups and Testing:
Verify all security groups are correctly configured to allow necessary traffic while restricting unauthorized access.
Test the setup by accessing the application through the ALB's DNS name.
For a visual demonstration and further insights, you may find the following Medium Article helpful:

