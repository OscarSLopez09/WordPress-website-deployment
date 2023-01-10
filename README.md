<h1>WordPress Deployment</h1>

 


In this project, I would deploy a WordPress website on AWS using a Three-Tier infrastructure. The AWS services used in this project are the following: 

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

**Create VPC:**

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.0.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

On the AWS console look for VPC, then I select the N Virginia US region.
* VPC Dashboard select: create VPC 
* Resources to create: VPC only 
* Name  tag: Dev VPC 
* IPv4 CIDR: 10.0.0.0/16 
* Tenancy: Default 
* Click create VPC
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.1.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

After the VPC has been created, I select acitons and edit VPC settings. On the DNS settings, I selected: Enabled DNS Hostnames and save it. <br/>

Internet Gateway creation: 
* On VPC Dashboard select Internet Gateways 
* Name tag: Dev IG 
* Select Create internet gateway 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.7.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

* Internet Gateway is attached to VPC by clicking on attach to VPC button. 
* Available VPCs - select Dev VPC and click on Attach Internet gateway.

In this section, I'm going to create two public subnets - Public Subnet AZ1 and Public Subnet AZ2. The public subnet are going to hold the Nat Gateways, set up Server, and jump Host in the later sections. 

**Public Subnets creation:** 

* On the VPC Dashboard select Subnets and click create subnet
* Create subnet VPC: Dev VPC 
* Subnet name: Public Subnet AZ1 
* Availability zone: US East N Virginia/ us-east-1a 
* IPv4 CIDR: 10.0.0.0/24 
* Click create Subnet 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.11.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Create subnet VPC: Dev VPC
* Subnet name: Public Subnet AZ2 
* Availability zone: US East N Virginia/ us-east-1b 
* IPv4 CIDR: 10.0.1.0/24 
* Click create Subnet

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.12.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>


I need to create a route table, With this RT I'm going to route traffic to the internet from the public subnets. The route tables are going to have a destination of 0.0.0.0/0 - target: Internet Gateway. Also, I associated the public subnets with the Public route table. 

To create route table, on VPC Dashboard select route tables.
 
Route table settings: 
* Name: Public RT 
* VPC: Dev VPC 
* Click Create route table 
* Click on routes – click Edit routes and click on Add routes 
* Destination: 0.0.0.0/0 
* Target: Internet Gateway (Dev IG) 
* Click Save changes 

We need to associate the public subnets with the Route table. 
* Select Subnets association and click on Edit subnet association. 
* Available Subnets: Public Subnet AZ1 and Public Subnet AZ2, select the two subnets. 
* Click Save association. 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.15.PNG?raw=true" height="50%" width="50%" alt="Disk Sanitization Steps"/>


Private subnets for this project: Priave App Subnet AZ1, Private Subnet AZ1, Private Data Subnet AZ1, and Private Data Subnet AZ2. The Private App Subnet are going to hold the Webserver and the Private Data Subnets are going to hold the Database instance. All AZ1 subnets are created on the US-East-1A and all the AZ2 subnets are created on the Us-East-1B AZ.

* Create subnet VPC: Dev VPC 
* Subnet name: Private App Subnet AZ1 
* Availability zone: US East N Virginia/ us-east-1a 
* IPv4 CIDR: 10.0.2.0/24 
* Click create Subnet 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.18A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Create subnet VPC: Dev VPC 
* Subnet name: Private App Subnet AZ2 
* Availability zone: US East N Virginia/ us-east-1b 
* IPv4 CIDR: 10.0.3.0/24 
* Click create Subnet 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.19.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Create subnet VPC: Dev VPC 
* Subnet name: Private Data Subnet AZ1 
* Availability zone: US East N Virginia/ us-east-1a 
* IPv4 CIDR: 10.0.4.0/24 
* Click create Subnet 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.20.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Create subnet VPC: Dev VPC 
* Subnet name: Private Data Subnet AZ2 
* Availability zone: US East N Virginia/ us-east-1b 
* IPv4 CIDR: 10.0.5.0/24 
* Click create Subnet 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/vpc.21.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>


A NAT gateway is a Network Address Translation (NAT) service. You can use a NAT gateway so that instances in a private subnet can connect to services outside the VPC. In the following section I’m going to create two Nat Gateways to connect the private subnets to the internet. One on each public Subnet, (Public AZ1, Public AZ2).


<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.1.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 

The Nat gateway is being created on the Public Subnet AZ1 and an elastic IP is being allocated.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.1A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>


 Private Route table (Private RT AZ1) is created with the destination: 0.0.0.0/0 target - Nat Gateway AZ1. On subnete association I associate the Private App Subnet AZ1 and Private Data AZ1. This is going to connect the private subnets with public subnets using the Nat Gateway:
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.2A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>


Now, I create a second Nat Gateway for the AZ2 subnets:

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.4.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

The sencond route table is created with the destination: 0.0.0.0/0 target - Nat Gateway AZ2 associated with the Private subnets: Private App Subnet AZ2 and Private Private Data Subnet AZ2. 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ng.4B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

In this part of the project, I’m going to create the security groups needed. I will create:

- Alb security group – Port = 80 and 443 Source = 0.0.0.0/0 

- SSH security group – Port 22 Source = my IP address 

- Webserver security group – Port = 80, 443, and 22 Source = ALB security group and SSH security group 

- Database Security group – Port = 3306 Source = Webserver Security group 

- EFS Security group – Port = 2049 and 22 Source = Webserver SG, SSH SG 



To create security groups, we go to VPC then security groups tab – we select Create Security Group.
 
security group Configuration settings:  
* Security group name: ALB SG 
* Inbound rules: (Http, 80) source (0.0.0.0/0) 
* Inbound rules: (Https, 443) source (0.0.0.0/0) 
* Create security group
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.1B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/albgr.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

SSH Security group:  
* name – SSH SG 
* Inbound rules (ssh, 22) source (my IP)
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.3.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>
 
Webserver Security Group:
* SG Name: Webserver SG 
* Inbound rules (Http, 80) source (ALB SG)
* Inbound rules (Https, 443) source (ALB SG) 
* Inbound rules (SSH, 22) source (SSH SG)
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/sg.4.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>
 
Database security group:
* SG Name: Database SG 
* Inbound rules (MySQL/Aurora, 3306) source (Webserver SG) 
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/dbsg.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

EFS security group:
* SG Name: EFS SG 
* Inbound rules (NFS, 2049) source (Webserver SG) 
* Inbound rule (SSH, 22) source (SSH SG)

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/efsgr.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
  
In this section of the project, I’m going to create the Database instance. The database is going to be a MySQL DB and is going to be hosted on the AZ North Virginia US –East –1B. Also, I’m going to create a Database subnet group. 
 

* To start we look for RDS, on the AWS management console, then on the left side we find subnet groups.
* Click on create DB subnet group
* Name: Database subnet, for the 
* Availability Zones: select US-East-1B and US-East-1A
* Subnets: Private Data subnet AZ1 and Private Data Subnet AZ2
* Click on Create 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.1A.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.2A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

Create Database configuration settings:
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.3.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Standard create: Engine option: MySQL
* Engine Version: 5.7.38
* Templates: Dev/Test
* DB instance Identifier: dev-rds-db
* DB username and password
* Connectivity on Virtual Private cloud (VPC): Dev VPC

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.4.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Database Subnet Group – security groups: Database SG 
* Additional configuration DB name: applicationdb
* Select Create Database 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rds.4E.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>


In this section of the project, I will create an EFS file system, so that Webserver can have access to shared files, application code, and application file configurations. The EFS Mount targets are in each AZ in the VPC. The Webservers will use the mount targets to connect to the EFS. 
 
On the AWS console look for EFS, select create file system, customize option. The file system settings will appear.
 
Configuration settings:
 
* Name: Dev-EFS
* Storage Class: Standard
* Enable encryption of data at rest: disabled
* Click on the next button. 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/efs.1.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>

* Network settings: Dev VPC
* Mount targets: select the AZ US-East-1A
* Subnet: Private Data Subnet AZ1
* security groups: EFS SG
* Second Mount target created: AZ US-East-1B 
* Subnet: Private Data Subnet AZ2
* Security groups: EFS SG. 
* Select next and click on Create. 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/efs.1A.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>


In this section, I will create an EC2 instance in the public subnet AZ1. In this instance, I will install the Webserver and move files to EFS. The EC2 instance will be used as a setup server. 


On the AWS console look for EC2, select launch instance:

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ss.1.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>

On the launch instance, I choose the following settings:
* Name: Setup Server
* Application OS: Amazon Linux 2 AMI
* Instance type: T2 Micro
* Key pair: Mosalah9 
* Networking: Dev VPC
* Subnet: Public Subnet AZ1
* Security groups: SSH SG, Webserver SG, and ALB SG – click on launch instance. 
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/ss.1C.PNG" height="20%" width="50%" alt="Disk Sanitization Steps"/>
 

In this section, I will begin the installation of WordPress on the Setup Server EC2. I’m connecting to the Setup server with Putty.  

1.  create the HTML directory and mount EFS to it.
* yum update -y 
* mkdir -p /var/www/html 
* sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-03c9b3354880b36a6.efs.us-east-1.amazonaws.com:/ /var/www/html 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.2.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>

2. install apache. 
* sudo yum install -y httpd httpd-tools mod_ssl 
* sudo systemctl enable httpd  
* sudo systemctl start httpd 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.3.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>
 

3. install php 7.4 
* sudo amazon-linux-extras enable php7.4 
* sudo yum clean metadata  
* sudo yum install php php-common php-pear -y  
* sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.4A.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>
  
4. install mysql5.7 

* sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm 
* sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022 
* sudo yum install mysql-community-server -y 
* sudo systemctl enable mysqld 
* sudo systemctl start mysqld 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.5.PNG" height="80%" width="80%" alt="Disk Sanitization Steps"/>
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.5A.PNG" height="80%" width="80%" alt="Disk Sanitization Steps"/>


5. set permissions. 
* sudo usermod -a -G apache ec2-user 
* sudo chown -R ec2-user:apache /var/www 
* sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \; 
* sudo find /var/www -type f -exec sudo chmod 0664 {} \; 
* chown apache:apache -R /var/www/html 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.6.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>

6. download wordpress files.
* wget https://wordpress.org/latest.tar.gz 
* tar -xzf latest.tar.gz 
* cp -r wordpress/* /var/www/html/ 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.7.PNG" height="70%" width="70%" alt="Disk Sanitization Steps"/>


7. create the wp-config.php file.
* cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
 
8. edit the wp-config.php file.
* nano /var/www/html/wp-config.php 
* On the wp-config.php, I input the following parameters: 
* DB name: applicationDB
* DB user: Bervatov08
* DB password: Arsenal25
* DB Host: dev-rds-db.cggzpwqbicm6.us-east-1.rds.amazonaws.com
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.8A.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

9. restart the webserver.
* service httpd restart 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.8B.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

After setting up the WordPress server. I copy the public IP of the Setup server and paste it to the URL bar, then at the end of the URL I add the (wp-admin) line and successfully login to the Server. 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/wp.9B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
 In this section of the project, I will create two EC2 instances: Webserver AZ1 and Webserver AZ2. These servers are going to have the configurations of the Setup server. Also, I’m going to create a target group and an ALB. The application load balancer is used to distribute traffic across EC2 instances in multiple availability zones. 


On the AWS console look for EC2, select launch instance. On the launch instance, I choose the following settings: 
* Name: Webserver AZ1 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.1.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Application OS: Amazon Linux 2 AMI 
* Instance type: T2 Micro 
* Key pair: Mosalah9 
* Networking settings: 
* VPC: Dev VPC 
* Subnet: Private App subnet AZ1 
* Security group: Webserver SG
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.1C.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
* Advanced Details: scroll down to (User data) and input the commands to create Webserver:

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.1E.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>


* Creating Webserver AZ2:
* Name: Webserver AZ2 
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Application OS: Amazon Linux 2 AMI 
* Instance type: T2 Micro
* Key pair: Mosalah9 
* Networking settings: 
* VPC: Dev VPC 
* Subnet: Private App subnet AZ2 
* Security group: Webserver SG 
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.2C.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 
* Advanced Details: Scroll down to user data and input the command to create Webserver:

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.2E.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>
 

To create a target group, go to the EC2 Dashboard and on the left side look for Target Groups tab and select it. Select create Target Groups. I used the following configuration: 

Configuration settings: 
* Choose a target type: Instance 
* Target Group Name: Dev-TG

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.3.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Protocol: HTTP –80 
* VPC: Dev-VPC 
* Register Targets: Webserver AZ1 and Webserver AZ2 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.3D.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

Select Include as pending below, then select Create Target Group.

To create a Load Balancer, go to the EC2 Dashboard and on the left side look for Load Balancer tab and select it. Select Create load balancer. I used the following configuration: 

* Application load balancer: Create 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.4.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Load balancer name: Dev-ALB 
* Scheme: Internet facing 
* Network mapping: 
* VPC: Dev VPC 
* Availability zone: US-East-1A subnet (Public Subnet AZ1) 
* Availability zone: US-East-1B subnet (Public Subnet AZ2)
* Security group:  ALB SG 
* Listener and routing: 
* Protocol: HTTP – 80  forward to: Dev-TG  (Target group)

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb.4C.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

* Finally, click Create load balancer. 

After creating the load balancer, I need to test. Using the load balancer DNS name, I open a new tab and copy and paste it. I’m able to access the Webservers.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb000.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

Next, I need to login as admin to update the URL address on the WordPress server. 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/alb001.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>



In this section of the project, I’m going to register a record set on Route 53.

On the AWS console look for Route 53, then on the Route 53 Dashboard select: DNS management 1 hosted zone. Select the hosted zone (Richarlison01.click), the hosted zone details page will be displayed, and I click on Create record. <br/>

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rt.1B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

Configuration settings: 
* Record name: www 
* Record type: A – Routes traffic to an IPv4 address and some AWS resources. 
* Alias record selected. 
* Route traffic to: Alias to Application and Classic load balancer 
* Choose a region: US-East-North Virginia 
* Choose Load balancer: dualstack.Dev-ALB-334537928.us-east-1.elb.amazonaws.com 
* Finally, click on Create records.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rt.1C.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

To test the record registration, I open a new browser tab and copy and paste the domain: www.Richarlison.click. The test is successful its sending me to WordPress. 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rt.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

Now, I need to login to WordPress as admin using the following URL: www.Richarlison.click/wp-admin . Every time we update the domain name, it needs to be updated on WordPress to properly connect. 

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/rt.3.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>



In this section, I will create an SSL certificate in AWS Certificate Manager. This is with the purpose of encrypting the traffic from our Webserver to the Web browser.
 
On the AWS console look for Certificate Manager, then select request certificate. On the certificate type select – Request a public certificate and click next. 

Configuration settings:
* Domain names – Fully qualify domain names: richarlison017.click.
* Validate method: DNS validation, scroll down and select Request.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/cr.3.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>
 
Now, I need to validate with Route 53 our domain name, this is done by clicking on Create records in Route 53, select the domain name (richarlison017.click) and select Create records. 
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/cr.3D.PNG" height="60%" width="60%" alt="Disk Sanitization Steps"/>

In this section of the project, I’m going to create a jump host/ jump box. The jump host is an EC2 instance that is going to be created in the public subnet and is going to be able to connect to the private subnets.
 

On the AWS console look for EC2, select launch instance. On the launch instance, I choose the following settings:
 
* Name: Bastion host.
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/bj.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* Application OS: Amazon Linux 2 AMI.
* Instance type: T2 Micro.
* Key pair: Mosalah9.
* Networking: Dev VPC.
* Subnet: Public Subnet AZ1.
* Security groups: SSH SG, then click on launch instance. 
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/bj.2B.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

A listener is a process that checks for connection requests. You define a listener when you create your load balancer, and you can add listeners to your load balancer at any time. In this section, I will create an HTTP Listener for the Application Load Balancer and secure the Website. 

On the AWS console look for EC2, then on the left side of the Dashboard look for Load balancer. Click on the load balancer (Dev-ALB), look for the listener section and select add listener.
 
<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/hl.2.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

* On the listener details change the protocols: HTTPS - 443.
* Default actions, select: Forward to – Dev-TG.
* Scroll down to Default SSL/TLS certificate – From ACM select (richarlison017.click) certificate. At this point click Add button.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/hl.4.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

Now, scroll down to Default SSL/TLS certificate – From ACM select (richarlison017.click) certificate. At this point click Add button.

<img src="https://github.com/OscarSLopez09/WordPress-website-deployment/blob/master/hl.5.PNG" height="50%" width="50%" alt="Disk Sanitization Steps"/>

Click on listener and select – HTTP:80. Now on the action menu select – Edit listener, go down to default actions – select redirect change the port: HTTPS: 443, then click on Save changes. 

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
 










