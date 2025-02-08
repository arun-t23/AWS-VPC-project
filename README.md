# AWS-VPC-project
Secure VPC Setup with EC2 Instances
# About the Project: VPC with public-private subnet in production
![image](https://github.com/user-attachments/assets/4331d1d5-22e6-4b90-884c-bc844c6df146)


# Implementation
* Create a VPC with 2 public and Private Subnets in different availability zones with NAT Gateways in each public subnet.
  * I got an error while creating the VPC saying that maximum number of Elastic IP have been reached. so I released some IP's. **What is _Elastic IP_?:** Lets say we have assigned EC2 Instance with an Elastic IP, even if the EC2 instance goes down and comes back the IP will remain same. It is a Static IP address because it never changes.
  * Here elastic IP is assigned to NAT gateway. **What is _NAT Gateway_:** It will mask the IP address of my application in the private subnet with the public IP address of NAT gateway.
* Create a Auto Scaling Group.
  * Auto scaling in AWS cannot be created directly, we can use launch template we can use this template in multiple autoscaling groups or this template act as reference to understand how autoscaling group is behaving.
  * Select the **OS Image-Ubuntu**, Instance type, in **security group - select the vpc that we created for the project**, and inbount traffic allow **Type-ssh** from **source type-anywhere**, add another security group for application deployed in the instances **Type-custom TCP**, **port - 8000**, Click Launch Template.
  * Now create a auto scaling by using the launch template, In network select the Vpc and private subnets because we want the instances in private subnet to auto scale, for this project we dont need load balancing. Select the desired capacity you need.
  * Now check the instances will be created in different availability zones.
# Install application inside the instaces.
  * Go to the private instaces and try login you cannot because you dont have the public IP thats because we have not given public IP to secure it. Then how do we login in to it?
  * **BASTION:** create a Bastion host Ec2. It acts as mediator between external users or public subnet. After creating Bastion access the private subnet from Bastion. While creating make sure the Bastion should be created in the same VPC. You need to have the keyvalue pair to be copied in the bastian Ec2 fro your PC by using secure copy
     > ```scp -i /Users/Downloads/practice.pem /Users/Downloads/practice.pem ubuntu@<publicip of bastion ec2>:/home/ubuntu```
  * Now ssh to bastion ```ssh -i ubuntu@<IP>``` you can see the pem file in the bastion if you dont see the pem you are unable login to the private subnets make sure you have the pem file copied.
  * 
    
