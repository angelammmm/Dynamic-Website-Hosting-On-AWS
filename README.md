Are you ready to take your website to the next level ? For this project I will be guiding you on how to build a 3 tier VPC from scratch using LAPM Stack to optimize your websites storage and networking performance. Using the following architecture I will show you how to deploy a website hosted on Amazon Workspace. In the prerequisite section I have listed the topics that you should be familiar with before starting. If some of the prerequisites are not familiar to you, no problem I will provide a glossary as well.

Prerequisites

Understanding of basic networking concepts such as DNS (Domain Name Service), Route tables, subnets and firewalls.
Knowledge of AWS services like VPC, Availability Regions, S3 Buckets, Application load balancer, EC2, and Route 53.
For this project a you are a consultant for a car rental company that wants to move their data from physical servers to the cloud. They want to host their website on AWS with high availability. And are needing to update their security policies to ensure their end users data is safe. We have created the following architecture.


Lets get started!

Step 1. We’ll start by selecting our availability zones we would like our VPC to be in. To change your region navigate to the top right hand corner of your console and click on the drop on and select the US East (N. Virginia) region.


Step 2. In the top left search box type VPC under services select VPC again. Under Virtual and Private cloud select your VPCs and select create your VPC in the right hand corner. Make sure to select VPC only and give your VPC a name. For this project I will name it “Dev VPC” and select IPv4 CIDR block. And for the IPv4 CIDR we will give it the IP address of 10.0.0.0/16 according to our architecture suggestion. And we will then select no IPV6 CIDR block and set the tendency as default then select create VPC.


A virtual private cloud (VPC) is a private digital space hosted on the cloud. A VPC uses CIDR IP adresses when it transfers data packets between connected devices so when users are using VPC’s it allows them to do it in a secure enviroment. For this project we did not select IPv6 block since this is just a demonstration but when creating a VPC that is going to be used long term it is preferred to use IPv6 since it can hold up to 1,028 times more IP addresses then IPv4.

Step 3. Once you have selected create VPC it will take you back to your VPC dashboard it is important to wait till it says available. Next we will want to enable DNS hostname (Domain Name Service) in order to translate IP addresses into site names. To do this we would select action and edit VPC settings then select DNS hostnames. Enable hostnames and save changes.


Step 4. Create a internet gateway for our VPC. Under Virtual Private Cloud select Internet Gateways. Then select create internet gateway and select an internet gateway name I have chosen “Dev Internet Gateway” and select internet gateway.


Now we will need to attach our internet gateway to our VPC to allow it to communicate with the internet. While on your internet gateway select attach to a VPC on the green banner. Under available VPC’s it will show the VPC we have made. Only one internet gateway can be attached to a VPC at a time.

Step 5. Enter our public subnets in our first availability zone. On the right hand corner select subnets then create subnet. And select Dev VPC for the subnet name we will want to name it “Public Subnet AZ1”. According to our architecture we will want it to be under the availability zone US East (N.Virginia) / us-east-1a. Under IPv4 cidr block we will want to enter 10.0.0.0/24 once you have entered the cider block select create subnet.


For the next subnet we will want to follow the same steps for the first subnet but we will create the subnet “Public Subnet AZ2”. For availability zone we will select US East (N.Virginia) / us-east-1b and enter the cidr block as 10.0.1.0/24 and select create subnet. Once we have created the public subnets we will want to enable auto assign IP settings so anytime we launch an EC2 instance it will get a public IPv4 address.


Select the check box next to the subnet name and click edit subnet settings for us-east-1a. Then select enable auto assign IPv4 address and do the same for our second subnet us-east01b.


Step 6. Create route table. On the left select route table. There should be a route table already made when we created our VPC, by default it is private. To create a public route table we will select create route table. And name it “Public Route Table” and select our Dev VPC and select create route table.


Now that we have created our route table we will add a public route to the table to allow the route table to forward traffic to the internet. By selecting the routes tab and select edit routes. In order for our table to route to the internet we will enter under destination 0.0.0.0/0 and enter the initials “igw” and our Dev internet gateway will appear and select save changes.


Step 7. Associate public subnets to the public route table, you can do this by selecting subnet association when in your public route table and selecting edit subnet associations.


After you will select Public subnet AZ1 and AZ2 by selecting the check box and selecting save changes. We have successfully associated the subnet to the route table.

Step 8. Create Private subnets by selecting subnets in the left hand column and select create subnets. And select our Dev VPC and name our private subnet according to our architecture we will call the first private subnet “Private App Subnet AZ1” with the IPv4 CIRD block of 10.0.2.0/24 in the availability zone US East (N. Virginia) / us-east-1a.


We will want to replicate this process for the remaining 3 private subnets based on the architecture. The subnet names are highlighted in blue for example, the next subnet would be called “Private App Subnet AZ2” and next to it is the IPV4 cidr block IP 10.0.3.0/24. Based on the architure if the subnet is within the orange rectangle. And in the availability zone US-east-1b. This method can be used for knowing what to enter when creating the remaining subnets.


To ensure all subnets have been made correctly select subnets in your VPC Dashboard and filter by name there you should every subnet associated to your VPC. All subnets should be listed to ensure all subnets are created in the correct availability zone please check the architecture above.

Understanding Route tables

It is important to note that we created public and private subnets and subnets associated with the Public Route Table we made can be accessed through the internet. For the default route table that we made is by default private and not accessible to the internet when we created the four private subnets we did not need to associate it to a route table manually. It automatically associated itself to the only private route table we have the original route table made when the VPC was created.

You can check this by navigating to route tables and there you will see all route table when selecting the public route table box. And selecting subnet associations our public subnets should be listed.


The same method can be applied for our private route table under explicite subnet associates all our private subnets should be listed.


Step 9- Create NAT Gateways in the public subnet, in order for the private subnets to access the internet. We will add a route from the Private App and Data Subnet from the Private Route Table AZ1 to route to the NAT Gateway to the internet gateway. By doing this we are “tunneling” a safe passage for our private subnets to communicate to the internet.


To associate the route table with the internet gateway we will navigate to the route table and click associations. And navigate to edit subnet associations and select Private App Subnet AZ1 as well as Private Data Subnet AZ1 and click save.


Once you have hit save, you will be directed to your VPC dashboard and will navigate to the left and click on Nat Gateways. Then click create NAT Gateway, and we will name it “NAT Gateway AZ2” and put the subnet in Public Subnet AZ2 and select public connection type. Then select allocate an elastic IP address to assign the NAT Gateway an IP address and select create NAT Gateway.


After we will create another NAT Gateway in public subnet AZ2 and add another route to our new Private Route Table AZ2 to route traffic through the internet gateway. By creating to separate NAT Gateways associated with Private and Public subnets we are limiting the threat of having a single point of failure from having only one gateway and ensuring reliability by having a second in a different availability zone


To create our next route table on your VPC dashboard select route tables, then click create route table in the right hand corner. This route table will be called “Private Route Table AZ2” and it will be put in our DEV VPC and select create route table.


Now that we have made our route table we will add a route to the route table to route traffic through the NAT Gateway in the public Subnet AZ2. To add a route to the route table we just made select routes then edit routes.

And select add route it is important to remember in order for our route table to access the internet it requires us to enter the IP address 0.0.0.0/0 under destination. For private subnets to connect to the internet this IP has to be entered as IPv4 internet traffic is for the first octets of the IP (0.0 the second half 0.0/0) is to the NAT device.

Under Target select NAT Gateway then NAT GAteway AZ1 since we wish to host our gateway in availability zone AZ2.


And click save changes now that we have saved this route we have successfully set our route table to route traffic through the NAT Gateway in availability zone AZ2 . The last step for this route table is to associate it to the two private subnets Private App Subnet AZ2 and Private Data Subnet AZ2.

Now we can associate our private subnets Private App Subnet AZ2 and Private Data Subnet AZ2 to our route table Private Route Table AZ2. By selecting Route Tables on our VPC dashboard and select our route table Private Route Table AZ2 and selecting subnet association's then click edit subnet associations.


Select our desired subnets Private App Subbet AZ2 and Private Data Subnet AZ2 and select save associations. We have now created a successful route for our private subnets in a second availability zone to access the internet through our newly made route table and NAT Gateway to ensure reliability.

Step 10- Create security groups. Whether you follow these specific security groups or make your own, it is essential that there are groups in place to practice best security policies. When creating websites in AWS it holds essential data to businesses such as customers information, or internal company data by creating these groups you ensure who is allowed to access data and how. By specifing what IP and Ports can be used when attempting to gain access.


The first group will be called ALB Security Group this group is intended for our application load balancer. That can be accessed through the internet since we have assigned it the IP address 0.0.0.0/0 and leave ports 80 and 443 open. So that data traveling between our webpage can be seen in plain text and will be encrypted when traveling through the internet gateway.

In order to create this group in the search bar on the left hand corner search VPC and security groups in the left column. To simplify filter by our Dev VPC and select create security group. We will want to name it “Application Load Balancer Security group’’ and use the same name for the description and select our Dev VPC.


Scroll down and select add inbound rule here we will specify that the type will be HTTP and our source can come from anywhere on the internet 0.0.0.0/0 . And add another inbound rule for HTTPS for port 443 and give it the default internet IP of 0.0.0.0/0 and press enter.


Scroll down again and select save groups, now we have successfully created our security group to control what traffic is allowed in and out of our VPC. Now we will create another security group by clicking on create security group on our dashboard again.


We will name is SSH Security Group and we will use this for the description as well. Then we will select our DEV VPC and add our inbound rule. The type will be SSH our source will be My IP to limit access to your IP address only scroll down and select save.


The last security group we will create is for our web server, and name it “Web Server Security Group” and copy that to the description as well then select our Dev VPC.


Under inbound rule the first one we will add will be HTTP under port 80 and we will limit the source traffic to the application load balancer security group. By selecting source and scroll down till you see security groups and select ALB Security Group. The next rule we will add is HTTPS under port 443 and we will select the ALB Security group to limit our traffic. The last group we will add is for SSH and limit access to our SSH security group under security groups.


Scroll down and select create security group these are the only groups we will need to create for inbound and outbound traffic. Now we will create a separate group for our database select create security group again. And name it Database Security group, along with the description name. With our Dev VPC selected we can create our inbound rule. But we want our type to be MySQL and Aurora since we will be hosting our database there. And limit our source to the web server security group and select save security group.


Now that all security groups have been made to control inbound and outbound traffic we can create our RDS instance as seen below.


Step 11. In your search bar type RDS and select RDS under services, before we can create our database in our private subnets we will need to create our subnet groups to identify were we want to create our database in. Select subnet groups in the left hand colum. Click create DB subnet group and name it “Database Subnets” as well as the description. Under VPC select our DEV VPC again under Add Subnets we will specify what subnets we want to create our RDS in.

Under availability zone we will select us-east-1a and us-east 1b and for subnets we will select our cidr block for our Private Data Subnet 10.0.4.0/24. And under us-east-1b our CIDR block is 10.0.5.0/24 make sure you have selected the correct subnets scroll down and hit create.


Next we can create our database on the left hand column select database and select create database. For our database we will select standard creation and under engine type we will select MySQL. And we will select version 5.7 select the newest version. Under version select Dev and Test since we are still building our architect.


For this project we will select create single DB instances but if you are going to be hosting a website for clients Multiple AZ DB is recommended for optimal availability. Under settings the identifier name will be dev-rds-db scroll down. Under master username and password it is important to pick one that you can remember and to not share it with anyone. After scroll down to instance class and select Burstable Classes and check of include previous generation classes under T2 micro.


After we will leave the storage as default scroll down until you see connectivity and select the drop down and choose our Dev VPC. Scroll down until you see subnet groups and make sure our Database Security group is selected. We will leave the rest as default until you reach VPC security group and we will select our database security group again and according to our architecture we will create the master DB in us-east-1b.


For best practice we will be using password authentication, in advanced settings for the initial database name. We can call it “Applicationdb” after scroll down and select create DB. It is normal for the database to become available after a few moment it should finish loading.

Note: You will see view credentials it is very important these credential's are not shared with anyone as they will be used to get access to our database.


Step 12. Create S3 buckets. When creating a dynamic website there are many ways to upload web files, we will be creating one to hold our websites original structure such as the website’s graphics, and fonts ect. And a second to hold object files for our websites “information data” an example would be retail information such as car prices and model information ect.

Bucket Name: Website-web-files
Region:US East (N.Virgina) us-east-1
Server side encryption:Enabled
The rest we will leave as default, after you can upload your website files within the S3 bucket as an upload.

Bucket 2.

Bucket Name: Retail-Information-Data
Region: US East (N.Virginia) us-east-1
Server Side Encryption: Enabled
Step 13. Create an IAM Role with S3 Policy, this role will allow our EC2 instance to download the web files from our S3 buckets. Navigate to IAM dashboard and select create create roles.

For our entity we will select AWS Service and our common use case will be EC2 instance for our server to automatically call upon other AWS services on our behalf. To do this we will need to give it the permission “Amazon S3 Full Access” and name the role S3 Role.


Step 14. Launch our EC2 in our public subnet and download our webfiles from our S3 bucket to install on our website.

Network: Dev VPC
Subnet:Public Subnet AZ1
IAM Role: S3 Role
Storage: Default General Purpose SSD
Tags: Setup Server
Security Groups: ALB Security Group, SSH Security Group, Webserver Security Group
Key Pair: Name Mysec2key, key pair RSA, file type .ppk
Step 15. Use Putty to launch instance once our EC2 instance has finished installing, open putty. For host name enter ec2user@(your EC2 IPV4 address) scroll down to authentication and select your key pair file.


Step 16. Once your instance is launched enter the following commands i have included the commands to update our instance. And general steps for how to install your web files. In the following commands i have put the word “Webfilenameexample” swap it out for your files name.

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
To check if you have entered the commands correctly go to your browser and enter the EC2 public IPV4 address. Your page should load MySQL enter your DB information.


To find this information navigate to our RDS dashboard under databases the database name dev-rds-db. There you will see endpoint name copy and paste it into host the port number will be under host name. After enter a password you can easily remember for the administrator. Once all the required information is filled to see your website select go to store.

Step 17. Create RDS key pair. Create a key pair with the name “Dummy-Key” and select type as .pem.

Step 18. Create another EC2 instance.

EC2 Name: Dummy Server
Instance Type: T2 Micro
Network: Dev Vpc
Subnets: Public Subnet AZ1
Security Group: SSH Security Group
Storage: General SSD
Key:Dummy-Key
Step 19. Upload desired files into your MySQL workbench and click administration tab, and select Data import/restore. You will be prompted to select your desired files. Once enter the target as aplicationdb and hit upload.

Step 20. Setup an Amazon Machine Image (AMI). We can use the AMI to launch new EC2 instances with our website already configured.

Navigate to our setup server EC2 instance and select create image. We will leave most of the settings as default except for the following specifications.


Image name: Car Rentals AMI

Key: Car Rentals AMI

Note: Wait until your snapshot says available before moving on. By creating this snapshot instead of having to create new EC2 instances and re entering the same commands we are saving ourselves time a snapshot captures our setup servers active state. Meaning all data is saved including our website setup and can be used as a backup.

Step 21. Clean up resource's that were used to upload our data to our MySQL database.

Terminate:Dummy Server, Dummy-Key Pair

Step 22. Create an application load balancer.


We will host our website on two EC2 instances in our private app subnets and will create a load balancer to distribute our web traffic.

Create a new EC2 instance under AMI image select My AMI’s and choose the AMI we made Car Rentals AMI.

Name:Web Server AZ1
Netowrk:Dev VPC
Subnets:Private App Subnet AZ1
Storage Default
Security Group: Web Server Security Group
Launch a second EC2 image with the same AMI image.

Name: Web Server AZ2
Network: Dev VPC
Subnet:Private App Subnet AZ2
Security Group: Web Server Security Group
Storage Default
Now we can create a target group by going to the left hand column under load balancing and select target groups. For configuration choose instances our target group name will be Dev target group our protocol will be HTTP under our Dev VPC.

Under health check enter the success code as 200,301,302. For our targets we want to select our EC2 instances we want to route traffic to.

Web Server AZ1 and Web Server AZ2

The last part of this step is to create a network load balancer on our EC2 dashboard under load balancing select load balancer.

Balancer type: Application Load Balancer
Load Balancer Name: Dev-ALB
Scheme: Internet Facing
IP Address Type: IPV4
VPC: Dev VPC
For our mapping we have to select at least one subnet. Select the following subnets.


After scroll down and remove the default security group and choose ALB Security Group.

For our listeners select HTTP protocol on port 80 with the target of DEV-TG. Ande select create balancer wait until it is active before moving on.

Clean Up: Now that we have successfully setup our webservers in both availability zones terminate our EC2 instance Setup Server.

Step 23. Register a Domain Name in Route 53, with our new EC2 instances when we copy and paste the IPV4 address into a browser search bar. We can view our website however we still need to register our domain so instead of searching an IP address we can type in our website name. And our website will automatically pop up.

To do this in our search bar in the AWS console search route 53 under and select get started. Our starting point will be to register a domain after search the domain you would like your website to be called.


I used the car rentals for our hypothetical client if your domain is not available a list will be provided of available domains to choose from that are similar. Once selected select proceed to check out and acknowledge the charges and enter your personnel information requested. It may take a few minute's for your domain name to become available.

Step 24. Create a record set in Route 53. Search Route 53 in our console and select our domain name and select create record.


Toggle Alias on and select Alias to Application and classic load balancer our region will be in the region we created the balancer in US East (N.Virgina). After choose the application load balancer we created Dev-ALB then create record.

Step 25. Register for an SSL Certification in order to encrypt all communication from the website to our servers. Search certificate manager under services and select request a public certificate by entering your domain name. Scroll down and select DNS Validation and hit request.

It will show as pending until we create a record set in Route 53 to validate this certificate. This can be done by selecting create records in Route 53 on your domains dashboard and select the domain name we are creating a record for and hit save. We have successfully validated this domain belongs to us.

Now customers can search carrentals.com instead of an IP address.

Step 26. Add a HTTP Listener. To create a listener go to your EC2 dashboard under load balancer select our Dev ALB.


Once you select add listener for our protocal we want HTTPS 443 and forward traffic to our Dev-TG. To specify we want to use our newly made certificate under Default Certificate select your domain name certificate and hit add.

In order to route traffic from HTTP to HTTPS for encryption our HTTP listener needs to be modified. By selecting edit on our load balancer and removing inbound actions default group. Specify you want to forward traffic from the HTTPS protocol on portm 443.


Note: To test if your SSL certificate has been applied search your domain name into a browser and a padlock should appear near the name.

Step 27. In order to ensure maximun reliability we will create an auto scalong group. By doing this we are ensuring our EC2 instances will grow or decrease based off of demand. We can accomplish this by creating a launch template with our custom AMI.

Go to the EC2 dashboard and select launch template and create.

Name:Dev-Launch Template
Auto Scaling Guidance: Enabled
Application and OS Images: Car Rentals AMI
Instance Type: T2 Micro
Key Pair: Myec2key
Security Groups: Web Server SG
Once you have saved the template go back to the EC2 dashboard under auto scaling select auto scaling groups. The template will be called “Dev-ASG”under our Dev launch template we just made.

Network:Dev VPC
Subnets: Private App Subnet AZ1 and Private App Subnet AZ2
Load Balancing Group: Dev-TG HTTP
The last part of this step is to specify our groups.

Desired Capacity 2- Meaning we want 2 EC2 instances running at all times.

Minimum -1 EC2 at minimum

Maximum- 4 EC2 instances running at once.


Leave the rest as default once you have created your application load balancer. To test that you have created the auto scalling group correctly. You can terminate our EC2 instances Web Server AZ1 and Web Server AZ2. With our balancer it will have created two more instances to take its place with the name “ASG-Webserver”. And our instances will automatically grow or decrease when needed automatically with our website pre confirgured.

Congratulations you have finished this project!

Glossary

DNS-Next we will want to enable DNS hostname (Domain Name Service) in order to translate IP addresses into site names. For example instead of having to type Amazons IP address to get to the website we would just enter the Amazon.com and automatically get routed to the website.

Route Table- Is a set of rules, known as routes that determines were network traffic gets directed. Each VPC (Virtual Private Cloud) is associated with a route table that controls the flow of traffic between subnets.

Public Subnet-Is a subnet associated with a route table that has a subnet to an internet gateway. This connects your VPC to the internet for example if a subnet is set to 10.0.0.0 then it can be accessed by the internet if it does not have a public IP then it is unavailable to those who try to access it from the internet.

Private Subnet- A private subnet that is routed to a route table without an internet gateway. Is not able to be accessed through the internet. And cannot reach out to the internet without administrator intervention. For enterprise companies it is critical that they are using private subnets to hold sensitive information such as app information and servers.

VPC (Virtual Private Cloud)- Is a virtual private network that is dedicated to designated cloud space that is isolated to hold specified EC2 instances, subnets, gateways and associated security groups.

Security Groups- Acts as a firewall that allows you to specify what traffic is allowed in and out of your VPC. You can specify which ports you have open or closed to allow for inbound or outbound traffic.

RDS-Amazon Relational Database Service (Amazon RDS) is a collection of managed services that makes it simple to set up, operate, and scale databases in the cloud. You can choose from popular databases such as MySQL.

S3 Buckets- Amazon S3 is an object storage service that stores data as objects within buckets. An object is a file and any metadata that describes the file. A bucket is a container for objects.

Load Balancer- handles incoming requests from users for information and other services. They sit between the servers that handle those requests and the internet. Once a request is received, the load balancer first determines which server in a pool is available and online and then routes the request to that server. Network Load Balancers and Classic Load Balancers are used to route TCP (or Layer 4) traffic.

Application Load Balancer- The application load balancer distributes incoming application traffic across multiple targets, such as EC2 instances, in multiple Availability Zones. This increases the availability of your application.

Route 53- Is Amazon Workspace’s built in Domain Name System used to register a Domain Name and DNS Routing.

Auto Scaling- AWS Auto Scaling monitors your applications and automatically adjusts capacity to maintain steady, predictable performance at the lowest possible cost. For example if your application receives an influx of data auto scaling will adjust your storage size to handle the data the same applies if your application has a drop in data.
