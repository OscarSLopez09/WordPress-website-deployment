<h1>WordPress Deployment</h1>

 

<h2>Description</h2>
In this project I would deploy a WordPress website on AWS using a Three-Tier infrastructure. The AWS services used in this project are the following: 

- Build AWS VPC from scratch. 
- Create Nat Gateways. 
- Create Security Groups. 
- Create RDS instance. 
- Create Elastic File System (EFS). 
- Install WordPress. 
- Create application load balancer. 
- Register a new domain name in Route 53. 
- Create a record set in Route 53. 
- Register an SSL certificate in AWS certificate manager. 
- Create an HTTPS Listener for the application load balancer.
<br />


<h2>Program walk-through:</h2>
I will start the project by creating a custom VPC. The architecture is divided into Three-Tier. On the first Tier we have the public subnets, on the second Tier I have the private subnets, and on the third Tear another private subnet that will hold the Database. Also, an internet Gateway as well as two route tables, one public and one private.

<p align="center">
Create custom VPC from scratch: <br/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.0.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 <p align="center">
 On the AWS console I select VPC, then I select the N Virginia US region. on the VPC Dashboard select create VPC and I input the configuration shown: 
 
 <p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.1.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

<p align="center">
After the VPC has been created, I select acitons and edit VPC settings. On the DNS settings, I selected: Enabled DNS Hostnames and save it. <br/>

<p align="center">
After the VPC is created I proceeded to create an Internet Gateway: <br/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.7.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

<p align="center">
 Internet Gateway is then attached to VPC: <br/>
<p align="center">
I create 2 subnents - Public Subnet AZ1 and Public Subnet AZ2. The public subnet are going to hold the Nat Gateways, set up Server, and jump Host in the later sections of the project. <br/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.11.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.12.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
 I create Route Table named: Public Route Table. With this RT I'm going to route traffic to the internet from the public subnets. The route tables are going to have a destination of 0.0.0.0/0 and target - Internet Gateway. Also, I associated the public subnets with the Public route table. <br/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.15.PNG?raw=true" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
I create the private subnets for this project: Priave App Subnet AZ1, Private Subnet AZ1, Private Data Subnet AZ1, and Private Data Subnet AZ2. The Private App Subnet are going to hold the Webserver and the Private Data Subnets are going to hold the Database instance. All AZ1 subnets are created on the US-East-1A and all the AZ2 subnets are created on the UsS -East-1B AZ.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.18A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.19.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.20.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.21.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
A NAT gateway is a Network Address Translation (NAT) service. You can use a NAT gateway so that instances in a private subnet can connect to services outside the VPC. In the following section I’m going to create two Nat Gateways to connect the private subnets to the internet. One on each public Subnet, (Public AZ1, Public AZ2).

<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.1.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
<p align="center"> 
The Nat gateway is being created on the Public Subnet AZ1 and an elastic IP is being allocated.
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.1A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
 Private Route table (Private RT AZ1) is created with the destination: 0.0.0.0/0 target - Nat Gateway AZ1. On subnete association I associate the Private App Subnet AZ1 and Private Data AZ1. This is going to connect the private subnets with publice subnets using the Nat Gateway: <br/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.2A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Now, I create a second Nat Gateway for the AZ2 subnets: <br/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.4.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

The sencond route table is created with the destination: 0.0.0.0/0 target - Nat Gateway AZ2 associated with the Private subnets: Private App Subnet AZ2 and Private Private Data Subnet AZ2. <br/>
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.4B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

In this part of the project, I’m going to create the security groups needed. I will create:

- Alb security group – Port = 80 and 443 Source = 0.0.0.0/0 

- SSH security group – Port 22 Source = my IP address 

- Webserver security group – Port = 80, 443, and 22 Source = ALB security group and SSH security group 

- Database Security group – Port = 3306 Source = Webserver Security group 

- EFS Security group – Port = 2049 and 22 Source = Webserver SG, SSH SG 


<p align="center">
To create security groups, we go to VPC then security groups tab – we select Create Security Group.  On create security group we fill in the information.  Security group name: ALB SG – Inbound rules (Http, 80) source (0.0.0.0/0) - (Https, 443) source (0.0.0.0/0), then create security group. 
 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.1B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
SSH Security group:  name – SSH SG, inbound rules (ssh, 22), source (my IP) 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.3.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
SG Name: Webserver SG, inbound rules (Http, 80) source (ALB SG) - inbound rules (Https, 443) source (ALB SG) - inbound rules (SSH, 22) source (SSH SG).
 
<p align="center"> 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.4.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center"> 
 
<p align="center">
SG Name: Database SG, inbound rules (MySQL/Aurora, 3306) source (Webserver SG). Also, I creted the EFS security group - SG Name: EFS SG, inbound rules (NFS, 2049) source (Webserver SG) - inbound rule (SSH, 22) source (SSH SG).
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.5.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/> 
 
<p align="center">
In this section of the project, I’m going to create the Database instance. The database is going to be a MySQL DB and is going to be hosted on the AZ North Virginia US –East –1B. Also, I’m going to create a Database subnet group. 
 
<p align="center">
To start we look for RDS, then on the left side we find subnet groups, then we click on create DB subnet group. I named it – Database subnet, for the Availability Zones – select US-East-1B and US-East-1A, subnets: Private Data subnet AZ1 and Private Data Subnet AZ2, and finally click on Create. 

<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.1A.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.2A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center"> 
<p align="center"> 
I select Create Database; the following configuration is selected: 
<p align="center">
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.3.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Standard create - Engine option: MySQL – Engine Version: 5.7.38 - Templates: Dev/Test - DB instance Identifier: dev-rds-db – Select the DB username and password – Connectivity on Virtual Private cloud (VPC): select Dev VPC
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.4.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
selected Database Subnet Group – on security groups: Database SG – On additional configuration I have to select a DB name: applicationdb -  Select Create Database. 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.4E.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>


In this section of the project, I will create an EFS file system, so that Webserver can have access to shared files, application code, and application file configurations. The EFS Mount targets are in each AZ in the VPC. The Webservers will use the mount targets to connect to the EFS. 
 
On the AWS console look for EFS, select create file system, customize option. The file system settings will appear and in general, create the name: Dev-EFS - Storage Class: Standard – On Enable encryption of data at rest: disabled – Click on the next button. 

<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/efs.1.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>

On network settings: Dev VPC – Mount targets: select the AZ US-East-1A – Subnet: Private Data Subnet AZ1 – security groups: EFS SG. I created a second Mount target: AZ US-East-1B – Subnet: Private Data Subnet AZ2 – Security groups: EFS SG. Then select Next and click on Create. 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/efs.1A.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>


In this section, I will create an EC2 instance in the public subnet AZ1. In this instance, I will install the Webserver and move files to EFS. The EC2 instance will be used as a setup server. 

<p align="center">
On the AWS console look for EC2, select launch instance:
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ss.1.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
On the launch instance, I choose the following settings:
<p align="center">
Name: Setup Server
<p align="center">
Application OS: Amazon Linux 2 AMI
<p align="center">
Instance type: T2 Micro
<p align="center">
Key pair: Mosalah9 
<p align="center">
Networking: Dev VPC
<p align="center">
Subnet: Public Subnet AZ1
<p align="center">
Security groups: SSH SG, Webserver SG, and ALB SG – click on launch instance. 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ss.1C.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>
 
<p align="center">
In this section, I will begin the installation of WordPress on the Setup Server EC2. I’m connecting to the Setup server with Putty.  
<p align="center">
1.  create the HTML directory and mount EFS to it.
<p align="center">
- yum update -y 
<p align="center">
- mkdir -p /var/www/html 
<p align="center">
- sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-03c9b3354880b36a6.efs.us-east-1.amazonaws.com:/ /var/www/html 
<p align="center"> 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.2.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<p align="center">
2. install apache. 
<p align="center">
- sudo yum install -y httpd httpd-tools mod_ssl 
<p align="center"> 
- sudo systemctl enable httpd  
<p align="center"> 
- sudo systemctl start httpd 
<p align="center"> 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.3.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>
 
<p align="center"> 
3. install php 7.4 
<p align="center"> 
- sudo amazon-linux-extras enable php7.4 
<p align="center"> 
- sudo yum clean metadata 
<p align="center"> 
- sudo yum install php php-common php-pear -y 
<p align="center"> 
- sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y
<p align="center"> 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.4A.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>
 
<p align="center">  
4. install mysql5.7 
<p align="center"> 
- sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm 
<p align="center"> 
- sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022 
<p align="center"> 
- sudo yum install mysql-community-server -y 
<p align="center"> 
- sudo systemctl enable mysqld 
<p align="center"> 
- sudo systemctl start mysqld 
<p align="center"> 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.5.PNG" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.5A.PNG" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p align="center">
5. set permissions 
<p align="center">
- sudo usermod -a -G apache ec2-user 
<p align="center">
- sudo chown -R ec2-user:apache /var/www 
<p align="center">
- sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \; 
<p align="center">
- sudo find /var/www -type f -exec sudo chmod 0664 {} \; 
<p align="center">
- chown apache:apache -R /var/www/html 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.6.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>

<p align="center">
6. download wordpress files 
<p align="center">
- wget https://wordpress.org/latest.tar.gz 
<p align="center">
- tar -xzf latest.tar.gz 
<p align="center">
- cp -r wordpress/* /var/www/html/ 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.7.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>

<p align="center"> 
7. create the wp-config.php file 
<p align="center">
- cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
 
 <p align="center">
 8. edit the wp-config.php file 
<p align="center">
- nano /var/www/html/wp-config.php 
<p align="center">
On the wp-config.php, I input the following parameters: 
<p align="center">
- DB name: applicationDB
<p align="center">
- DB user: Bervatov08
<p align="center">
- DB password: Arsenal25
<p align="center">
- DB Host: dev-rds-db.cggzpwqbicm6.us-east-1.rds.amazonaws.com
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.8A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
9. restart the webserver 
<p align="center">
- service httpd restart 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.8B.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

After setting up the WordPress server. I copy the public IP of the Setup server and paste it to the URL bar, then at the end of the URL I add the (wp-admin) line and successfully login to the Server. 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.9B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
 In this section of the project, I will create two EC2 instances: Webserver AZ1 and Webserver AZ2. These servers are going to have the configurations of the Setup server. Also, I’m going to create a target group and an ALB. The application load balancer is used to distribute traffic across EC2 instances in multiple availability zones. 

<p align="center">
On the AWS console look for EC2, select launch instance. On the launch instance, I choose the following settings: 
<p align="center">
Name: Webserver AZ1 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.1.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Application OS: Amazon Linux 2 AMI 
<p align="center">
Instance type: T2 Micro 
<p align="center">
Key pair: Mosalah9 
<p align="center">
Networking settings: 
<p align="center">
VPC: Dev VPC 
<p align="center">
Subnet: Private App subnet AZ1 
<p align="center">
Security group: Webserver SG
<p align="center"> 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.1C.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Advanced Details: scroll down to User data.
<p align="center">
User data: 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.1E.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Creating Webserver AZ2: <br/>
<p align="center">
Name: Webserver AZ2 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Application OS: Amazon Linux 2 AMI 
<p align="center">
Instance type: T2 Micro
<p align="center"> 
Key pair: Mosalah9 
<p align="center">
Networking settings: 
<p align="center">
VPC: Dev VPC 
<p align="center">
Subnet: Private App subnet AZ2 
<p align="center">
Security group: Webserver SG 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.2C.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Advanced Details: Scroll down to user data.
<p align="center">
User data: 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.2E.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
<p align="center">
To create a target group, go to the EC2 Dashboard and on the left side look for Target Groups tab and select it. Select create Target Groups. I used the following configuration: 
<p align="center">
Basic configuration:
<p align="center">  
Choose a target type: Instance 
<p align="center">
Target Group Name: Dev-TG
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.3.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Protocol: HTTP –80 
<p align="center">
VPC: Dev-VPC 
<p align="center">
Register Targets: 
<p align="center">
Webserver AZ1 and Webserver AZ2 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.3D.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Select Include as pending below, then select Create Target Group.

To create a Load Balancer, go to the EC2 Dashboard and on the left side look for Load Balancer tab and select it. Select Create load balancer. I used the following configuration: 
<p align="center">
Application load balancer: Create 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.4.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Load balancer name: Dev-ALB 
<p align="center">
Scheme: Internet facing 
<p align="center">
Network mapping: 
<p align="center">
VPC: Dev VPC 
<p align="center">
Availability zone: US-East-1A subnet (Public Subnet AZ1) 
<p align="center">
Availability zone: US-East-1B subnet (Public Subnet AZ2)
<p align="center">
Security group:  ALB SG 
<p align="center">
Listener and routing: 
<p align="center">
Protocol: HTTP – 80  forward to: Dev-TG  (Target group)
<p align="center"> 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.4C.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p align="center">
Finally, click Create load balancer. 
<p align="center">
After creating the load balancer, I need to test. Using the load balancer DNS name, I open a new tab and copy and paste it. I’m able to access the Webservers.
<p align="center">  
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb000.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p align="center"> 
Next, I need to login as admin to update the URL address on the WordPress server. 
<p align="center"> 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb001.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>


<p align="center">
In this section of the project, I’m going to register a record set on Route 53. 
<p align="center">
On the AWS console look for Route 53, then on the Route 53 Dashboard select: DNS management 1 hosted zone. Select the hosted zone (Richarlison01.click), the hosted zone details page will be displayed, and I click on Create record. <br/>
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rt.1B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center"> 
Configuration used: 
<p align="center">
Record name: www 
<p align="center">
Record type: A – Routes traffic to an IPv4 address and some AWS resources. 
<p align="center">
Alias record selected. 
<p align="center">
Route traffic to: Alias to Application and Classic load balancer 
<p align="center">
Choose a region: US-East-North Virginia 
<p align="center">
Choose Load balancer: dualstack.Dev-ALB-334537928.us-east-1.elb.amazonaws.com 
<p align="center">
Finally, click on Create records.
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rt.1C.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
To test the record registration, I open a new browser tab and copy and paste the domain: www.Richarlison.click. The test is successful its sending me to WordPress. 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rt.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Now, I need to login to WordPress as admin using the following URL: www.Richarlison.click/wp-admin . Every time we update the domain name, it needs to be updated on WordPress to properly connect. 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rt.3.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>



In this section, I will create an SSL certificate in AWS Certificate Manager. This is with the purpose of encrypting the traffic from our Webserver to the Web browser.
 
On the AWS console look for Certificate Manager, then select request certificate. On the certificate type select – Request a public certificate and click next. 
<p align="center">
Following settings configure:
<p align="center">
Domain names – Fully qualify domain names: richarlison017.click.
<p align="center">
Validate method: DNS validation, scroll down and select Request.
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/cr.3.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p align="center">
Now, I need to validate with Route 53 our domain name, this is done by clicking on Create records in Route 53, select the domain name (richarlison017.click) and select Create records. 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/cr.3D.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

In this section of the project, I’m going to create a jump host/ jump box. The jump host is an EC2 instance that is going to be created in the public subnet and is going to be able to connect to the private subnets.
 
<p align="center">
On the AWS console look for EC2, select launch instance. On the launch instance, I choose the following settings:
<p align="center">
Name: Bastion host
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/bj.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Application OS: Amazon Linux 2 AMI
<p align="center">
Instance type: T2 Micro
<p align="center">
Key pair: Mosalah9
<p align="center">
Networking: Dev VPC
<p align="center">
Subnet: Public Subnet AZ1
<p align="center">
Security groups: SSH SG, then click on launch instance. 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/bj.2B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

A listener is a process that checks for connection requests. You define a listener when you create your load balancer, and you can add listeners to your load balancer at any time. In this section, I will create an HTTP Listener for the Application Load Balancer and secure the Website. 

On the AWS console look for EC2, then on the left side of the Dashboard look for Load balancer. Click on the load balancer (Dev-ALB), look for the listener section and select add listener.
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/hl.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
On the listener details change the protocols: HTTPS - 443, then on default actions, select: Forward to – Dev-TG. Now, scroll down to Default SSL/TLS certificate – From ACM select (richarlison017.click) certificate. At this point click Add button.
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/hl.4.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Now, scroll down to Default SSL/TLS certificate – From ACM select (richarlison017.click) certificate. At this point click Add button.
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/hl.5.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<p align="center">
Click on listener and select – HTTP:80. Now on the action menu select – Edit listener, go down to default actions – select redirect change the port: HTTPS: 443, then click on Save changes. 
<p align="center">
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/hl.7.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
A launch template specifies instance configuration information - It includes the ID of the Amazon Machine Image (AMI), the instance type, a key pair, security groups, and other parameters used to launch EC2 instances. In this section I will create a launch template and an Auto Scaling group. This will provide availability to the Website. 

**To create a Lunch Template:**

* On the AWS console look for EC2, then on the left side of the Dashboard look for Launch Templates, then select Create launch templates. 
* Launch template name: Dev-Launch-Template.
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
* Auto scaling guidance – select Provide guidance to help me set up a template that I can use with EC2 auto.
* Application and OS: Amazon Linux 2 AMI.
* Instance type: T2 Micro.
* Key pair: Mosalah9.
* Firewall (Security and groups) select existing security group: Webserver SG.
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.5.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
* Scroll down to Advanced details (User Data), Then input the commands to create the Webserver and click on the Create launch template. 
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.7.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>


**To create an Auto Scaling Group:**
 
* On the left side of the EC2 Dashboard look for Auto scaling groups. Select Create Auto scaling group.
* Auto scaling group name: Dev-ASG.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.9A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
* Select launch template: Dev-Launch-Template and click Next.
* Network section VPC: Dev VPC.
* Availability zones and subnets: Private App Subnet AZ1 and Private App Subnet AZ2 and click Next.
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.9C.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
* Load balancing – select Attached to an existing Load balancer.
* Existing Load balancer target groups: Dev-TG.
* Health Check type – Select ALB, then click Next.
* On Desired Capacity: 2 Minimum Capacity: 1 Maximum Capacity: 4 then select Next.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.9G.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
* Select tags: ASG-Webserver and click Create Auto scaling group. 
 
Now I’m going back to the EC2 Instances and verify that the Auto Scaling is working.
 
The ASG is working the servers with the ASG-Webserver are initializing.
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.10.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>
 
This time I’m going to test if the servers are working by connecting to the website  www.Richarlison017.click.
 
As you can see, the test is successfull I'm able to contact the Webserver.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/asg.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 








### Create API

**To create the API**
1. Go to API Gateway console
2. Click Create API

![create API](./images/create-api-button.jpg) 

3. Scroll down and select "Build" for REST API

![Build REST API](./images/build-rest-api.jpg) 

4. Give the API name as "DynamoDBOperations", keep everything as is, click "Create API"

![Create REST API](./images/create-new-api.jpg)

5. Each API is collection of resources and methods that are integrated with backend HTTP endpoints, Lambda functions, or other AWS services. Typically, API resources are organized in a resource tree according to the application logic. At this time you only have the root resource, but let's add a resource next 

Click "Actions", then click "Create Resource"

![Create API resource](./images/create-api-resource.jpg)

6. Input "DynamoDBManager" in the Resource Name, Resource Path will get populated. Click "Create Resource"

![Create resource](./images/create-resource-name.jpg)

7. Let's create a POST Method for our API. With the "/dynamodbmanager" resource selected, Click "Actions" again and click "Create Method". 




<br />
<br />

</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
