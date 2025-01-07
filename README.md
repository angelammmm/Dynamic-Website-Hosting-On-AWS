This guide outlines the process of building a 3-tier Virtual Private Cloud (VPC) in Amazon Web Services (AWS) using a LAMP Stack (Linux, Apache, MySQL, PHP).In order to manage and install a website on a EC2 Server. 

<h2> Prerequisites:</h2>

- <b>Basic understanding of networking concepts: DNS, route tables, subnets, and firewalls.</b>
-[Familiarity with AWS services: VPC, EC2, RDS, and Application Load Balancer.]<b><i>

Architecture Overview
![Demo](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*5EtHb4fY-L2S7hsD9yuUzA.png)


<h2> The architecture consists of:</h2>

-<b>Presentation Tier - Public subnets hosting a bastion (jump) host for secure SSH access.</b>

-<b>Application Tier - Private subnets hosting EC2 instances running Apache and PHP.</b>

-<b>Data Tier - Private subnets with RDS instances for MySQL.</b>

Steps to Build the 3-Tier VPC

 <h2>1.Create a VPC</h2>

Define a VPC with CIDR block: 10.0.0.0/16

Enable DNS hostnames for the VPC.

<h2> 2.Set Up Subnets</h2>

Create public subnets for bastion host in multiple availability zones:

Public Subnet 1: 10.0.1.0/24

Public Subnet 2: 10.0.2.0/24

Create private subnets for application servers and databases:

Private App Subnet 1: 10.0.3.0/24

Private App Subnet 2: 10.0.4.0/24

Private DB Subnet 1: 10.0.5.0/24

Private DB Subnet 2: 10.0.6.0/24

![Subnets](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*aZOiS32lrN2F53PSKB3oDg.png)

<h2>3.Configure Internet and NAT Gateways</h2>

Attach an Internet Gateway to the VPC.

Set up a NAT Gateway in the public subnet to allow private subnets to access the internet securely.

<h2> 4.Establish Route Tables</h2>

Create and associate a public route table with public subnets, routing traffic through the Internet Gateway.

Create private route tables for application and database subnets, routing internet-bound traffic through the NAT Gateway.

<h2> 5.Launch EC2 Instances</h2>

Deploy a bastion host in the public subnet for SSH access.

Deploy application servers in private subnets with security groups configured for:

HTTP access from the Load Balancer.

SSH access from the bastion host.

<h2> 6.Install LAMP Stack on Application Servers</h2>

SSH into application servers via the bastion host.

Install Apache, PHP, and required modules:
#1. update ec2 instance
sudo su
sudo yum update -y
#2. install apache 
sudo yum install -y httpd httpd-tools mod_ssl
sudo systemctl enable httpd 
sudo systemctl start httpd
#3. install php 7.4
sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y
#4. install mysql5.7
sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo rpm - import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld
#5. set permissions
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
#6. download the webfiles from s3 to the html derectory on the ec2 instance
sudo aws s3 sync s3://aosnotes77-fleetcart-web-files /var/www/html
#7. unzip the Webfile folder
cd /var/www/html
sudo unzip (webfilename).zip
#8. move all the files and folder from the Webfile directory to the html directory
sudo mv Webfilename/* /var/www/html
#9. move all the hidden files from the Webfile Example Name diretory to the html directory

#10. enable mod_rewrite on ec2 linux, add apache to group, and restart server
sudo sed -i '/<Directory "\/var\/www\/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf
chown apache:apache -R /var/www/html 
sudo service httpd restart
Configure Apache to serve your application.

<h2>7. Set Up RDS for Database Layer</h2>

Create a DB subnet group encompassing private database subnets.

Launch an RDS instance with MySQL:

Security groups allow connections only from application servers.

 <h2>8.Configure Application Load Balancer (ALB)</h2>

Set up an ALB in the public subnets.

Ensure ALB's security group allows inbound HTTP traffic.

Register application servers with the ALB's target group.

 <h2>9.Finalize Security Groups and Testing</h2>

Verify security group configurations to allow necessary traffic while restricting unauthorized access.

Test the application via the ALB DNS name. To ensure your website is viewable with your unique DNS Name.  


For a visual demonstration and further insights, you may find the following Medium Article helpful:(https://medium.com/devops-dev/how-to-build-a-3-tier-vpc-in-aws-using-lamp-stack-dcb66b7095bd)


