This guide outlines the process of building a 3-tier Virtual Private Cloud (VPC) in Amazon Web Services (AWS) using a LAMP Stack (Linux, Apache, MySQL, PHP).In order to manage and install a website on a EC2 Server. 

Prerequisites

Basic understanding of networking concepts: DNS, route tables, subnets, and firewalls.

Familiarity with AWS services: VPC, EC2, RDS, and Application Load Balancer.

Architecture Overview

The architecture consists of:

Presentation Tier - Public subnets hosting a bastion (jump) host for secure SSH access.

Application Tier - Private subnets hosting EC2 instances running Apache and PHP.

Data Tier - Private subnets with RDS instances for MySQL.

Steps to Build the 3-Tier VPC

1. Create a VPC

Define a VPC with CIDR block: 10.0.0.0/16

Enable DNS hostnames for the VPC.

2. Set Up Subnets

Create public subnets for bastion host in multiple availability zones:

Public Subnet 1: 10.0.1.0/24

Public Subnet 2: 10.0.2.0/24

Create private subnets for application servers and databases:

Private App Subnet 1: 10.0.3.0/24

Private App Subnet 2: 10.0.4.0/24

Private DB Subnet 1: 10.0.5.0/24

Private DB Subnet 2: 10.0.6.0/24

3. Configure Internet and NAT Gateways

Attach an Internet Gateway to the VPC.

Set up a NAT Gateway in the public subnet to allow private subnets to access the internet securely.

4. Establish Route Tables

Create and associate a public route table with public subnets, routing traffic through the Internet Gateway.

Create private route tables for application and database subnets, routing internet-bound traffic through the NAT Gateway.

5. Launch EC2 Instances

Deploy a bastion host in the public subnet for SSH access.

Deploy application servers in private subnets with security groups configured for:

HTTP access from the Load Balancer.

SSH access from the bastion host.

6. Install LAMP Stack on Application Servers

SSH into application servers via the bastion host.

Install Apache, PHP, and required modules:

sudo yum update -y
sudo yum install httpd php php-mysql -y
sudo systemctl start httpd
sudo systemctl enable httpd

Configure Apache to serve your application.

7. Set Up RDS for Database Layer

Create a DB subnet group encompassing private database subnets.

Launch an RDS instance with MySQL:

Security groups allow connections only from application servers.

8. Configure Application Load Balancer (ALB)

Set up an ALB in the public subnets.

Ensure ALB's security group allows inbound HTTP traffic.

Register application servers with the ALB's target group.

9. Finalize Security Groups and Testing

Verify security group configurations to allow necessary traffic while restricting unauthorized access.

Test the application via the ALB DNS name.


For a visual demonstration and further insights, you may find the following Medium Article helpful:

