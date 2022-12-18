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
I will start the project by creating a custom VPC. The architecture is divided into Three-Tier. On the first Tier we have the public subnets, on the second Tier I have the private subnets, and on the third Tear the Database created on private subnets. Also, an internet Gateway would be created on the VPC. 

<p align="center">
Create custom VPC from scratch: <br/>
<img src="https://i.imgur.com/5qTtFDQ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/zAhSh9E.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/4KfYhYm.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
We enabled VPC hostname on the VPC: <br/>

<img src="https://i.imgur.com/bx1ePuf.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Internet Gateway creation: <br/>
<img src="https://i.imgur.com/4vfJCrB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Internet Gateway is attached to VPC: <br/>
<img src="https://i.imgur.com/Sw8kHhe.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/wPP5Bxc.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Public subnets creation named: Public Subnet AZ1 and Public Subnet AZ2 <br/>
<img src="https://i.imgur.com/jDs8uzf.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Hmmi6Dj.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/C0gEDBC.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
 Public Subnet AZ2: <br/>
<img src="https://i.imgur.com/Hmmi6Dj.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/sQhiAGy.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>


<p align="center">
Enabled Auto-assign IP settings on both AZ1 and AZ2: <br/>
<img src="https://i.imgur.com/WJ5ajlB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Glxr7BV.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Creating Route Table named: Public Route Table <br/>

<img src="https://i.imgur.com/xk4wcEx.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Vg4ZWlj.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Adding public routes to the table. Public routes allow access to the internet. <br/>

<img src="https://i.imgur.com/LDTRyf8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/sy9yPEg.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/OIQ4q8B.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<p align="center">
Adding private subnets to the VPC: <br/>

<img src="https://i.imgur.com/VzApukE.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/ZzfLhb4.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/WJLylYc.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

A NAT gateway is a Network Address Translation (NAT) service. You can use a NAT gateway so that instances in a private subnet can connect to services outside the VPC. In the following section I’m going to create two Nat Gateways to connect the private subnets to the internet. 

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
