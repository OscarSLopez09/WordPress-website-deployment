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
Launch the utility: <br/>
<img src="https://i.imgur.com/5qTtFDQ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
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
