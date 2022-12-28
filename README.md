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

<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
I will proceed to create the MySQL Database instance in the private subnet: 

<img src="https://i.imgur.com/L2ZIDEx.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/67XeRmj.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/eJBK26g.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/t5323c2.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/R6PVXmS.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/ZAwu1A7.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/yDp2fOn.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/I0CXiGU.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/50k0qBZ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/5xlM0EE.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Xsd4bKB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/2JWDYj6.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

In this section of the project, I will create an EFS file system, so that Webserver can have access to shared files. The EFS Mount targets are in each AZ in the VPC. The Webservers will use the mount targets to connect to the EFS. 

<p align="center">
<img src="https://i.imgur.com/ISA8XqJ.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/61syv8Z.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/HcOsrJQ.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/iJ0qDqQ.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>

In this section, I will create an EC2 instance in the public subnet to install the Website and move files to EFS. The EC2 instance will be used as a setup server. 

<p align="center">
<img src="https://i.imgur.com/zoQsmrA.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/4DE3NxF.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Wbe42U0.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/8eyv70j.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/gc7ltAS.png" height="20%" width="50%" alt="Disk Sanitization Steps"/>

Now, I will begin the installation of the WordPress site and move the files to EFS. I’m connecting over SSH using CMD. I will start by creating the HTML directory and mount the EFS to it. 

<p align="center">
<img src="https://i.imgur.com/psO4Oaw.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/YhtRb9R.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

<p align="center">
Apache installation: <br/>
<img src="https://i.imgur.com/cVxLtXH.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/fBq150P.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

<p align="center">
Installing PHP 7.4: <br/>
<img src="https://i.imgur.com/DKXLOg1.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/4hbs6th.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/5irYKcD.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

<p align="center">
Installing MySQL 5.7 <br/>
<img src="https://i.imgur.com/AKsAcO7.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/m4IV6Og.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/67MSN9p.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

<p align="center">
Set the permissions: <br/>
<img src="https://i.imgur.com/nnGJi8Y.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

<p align="center">
Download WordPress files: <br/>
<img src="https://i.imgur.com/HTJQGwC.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

<p align="center">
Edit the wp-config.php file: <br/>
<img src="https://i.imgur.com/SBdPbSY.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/CuqYx0o.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

<p align="center">
Restart the Webserver: <br/>
<img src="https://i.imgur.com/wuxt3R5.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/9Yvrns0.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/rGgttZD.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/csMJrra.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>


Application Load Balancer is used to distribute web traffic across EC2 instances in multiple AZs. In this section I will create two EC2 instances on each of the private subnets. 

<p align="center">
Creating Webserver AZ1: <br/>
<img src="https://i.imgur.com/WNB1lej.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/tEsOlrQ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/5ZxFjhz.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/kA0BTP0.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/M0IwlA2.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/youDzEV.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/6VWiSnl.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Creating Webserver AZ2: <br/>
<img src="https://i.imgur.com/PEyj7NM.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/zEC7lPE.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/p8sbkzh.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/f424nbR.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Zfqqd1Y.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/ifCD5wi.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/yJSnZKg.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Creating target group for the ALB: <br/>
<img src="https://i.imgur.com/awR0EEA.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/uQkbKdh.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/BkD9SZd.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/wYNgh2w.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/VKlZIKW.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/OQVy8qF.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Creating the Application Load Balancer: <br/>
<img src="https://i.imgur.com/JrfTtaj.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/nNP2nH5.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/MP6p5Z9.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/TBNxaiR.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/CvZk0kT.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
In this section I will create a domain name and register a record set on Route 53: <br/>

<img src="https://i.imgur.com/iwagwqX.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/MFiaG4R.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/B79xpzZ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/4JWNZtp.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>

In this section, I will create an SSL certificate in AWS Certificate Manager. This is with the purpose of encrypting the traffic from our Webserver to the Web browser. 


<p align="center">
<img src="https://i.imgur.com/nQtTqYc.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/ueTaSxV.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/uEkkRLz.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/SdjaKfx.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/sCGMz2G.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/br8EeS3.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/G5n6iLL.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/XYfUR9E.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

To SSH into the instances in the private subnets. I will launch an EC2 instance in the public subnet, then from the instance in the public subnet, we can SSH into any instance in the private subnet. 

<p align="center">
Creating a Bastion Host to SSH into the private EC2 instances: <br/>

<img src="https://i.imgur.com/aIXNUKA.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/rH7xr0m.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Z14tzZJ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Z6UNhoi.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/yecestg.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>


A listener is a process that checks for connection requests. You define a listener when you create your load balancer, and you can add listeners to your load balancer at any time. In this section, I will create an HTTP Listener for the Application Load Balancer and secure the Website. 


<p align="center">
<img src="https://i.imgur.com/KZkX56B.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/xnHPwJK.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/SeDo2gx.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/iz7qsgU.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/b9S6TyJ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/zTzo8SQ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/EOnn1q8.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

A launch template specifies instance configuration information - It includes the ID of the Amazon Machine Image (AMI), the instance type, a key pair, security groups, and other parameters used to launch EC2 instances. In this section I will create a launch template and an Auto Scaling group. This will provide availability to the Website. 

<p align="center">
Creating Launch template: <br/>

<img src="https://i.imgur.com/CzN8ZOn.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/8wjnkqU.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/HN6BEnM.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/wkjNoAH.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/FQaJBjB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/TNQSnij.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/aPzpZhv.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/ApFEJlQ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>


<p align="center">
Creating the Auto Scaling Group: <br/>


<img src="https://i.imgur.com/plQKkZV.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Osxu19q.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/KHXFaPK.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/BOnZS6s.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/jDAhm0V.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/473ugvS.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/snM0fm4.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/1jSNEwJ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/rLc4drg.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/6rLPCBf.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/d11sMCl.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/FmLLR0C.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/XXqZBZd.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/rZHN8bo.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>




















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
