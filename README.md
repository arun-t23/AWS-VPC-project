# AWS VPC Project

## Secure VPC Setup with EC2 Instances

### About the Project: VPC with Public-Private Subnets in Production
![VPC Architecture](https://github.com/user-attachments/assets/4331d1d5-22e6-4b90-884c-bc844c6df146)

## Implementation

### 1. Create a VPC with Public and Private Subnets
- Create a VPC with **2 public and 2 private subnets** (A and B) in different availability zones.
- Deploy **NAT Gateways** in each public subnet.
- Uncheck the **S3 as endpoint**, as it is not required for this project.
- If you encounter an error regarding **maximum Elastic IPs reached**, release some IPs.

#### What is **Elastic IP**?
- An Elastic IP is a **static public IP** assigned to an instance. Even if the instance restarts, the IP remains the same.
- Elastic IP is assigned to the **NAT Gateway** to provide internet access to private instances.

#### What is **NAT Gateway**?
- It **masks the IP** of applications in private subnets by using the NAT gateway’s public IP.

### 2. Create an Auto Scaling Group
- Auto Scaling cannot be created directly; instead, we use a **Launch Template**.
- This template can be reused across multiple auto-scaling groups.

#### Steps:
1. Select **OS Image - Ubuntu**.
2. Choose an **Instance type**.
3. In **Security Group**, select the VPC created for this project.
4. Configure **Inbound Rules**:
   - **SSH (Type: SSH, Source: Anywhere)**.
   - **Custom TCP (Port: 8000)** for the application.
5. Create the **Launch Template**.
6. Create the **Auto Scaling Group** using the template.
7. In **Network Settings**, select the VPC and **private subnets**.
8. Set the desired instance capacity.
9. Verify that instances are created across different **Availability Zones**.

### 3. Access Private Instances Using a Bastion Host
- Private instances **do not have public IPs**, so we cannot SSH into them directly.

#### What is **Bastion Host**?
- A **Bastion Host** is an EC2 instance in the **public subnet** that allows secure access to instances in the private subnet.
- It acts as a **mediator** between external users and private instances.

#### Steps to Access Private Instances:
1. **Create a Bastion Host EC2** in the same VPC.
2. Copy the **private key** from your local machine to the Bastion EC2:
   ```bash
   scp -i /Users/Downloads/practice.pem /Users/Downloads/practice.pem ubuntu@<public-ip-of-bastion>:/home/ubuntu
   ```
3. SSH into the Bastion host:
   ```bash
   ssh -i practice.pem ubuntu@<public-ip>
   ```
4. From the Bastion, SSH into a **private instance**:
   ```bash
   ssh -i practice.pem ubuntu@<private-ip>
   ```
5. If you get a **permission error**, change the file permissions:
   ```bash
   chmod 400 practice.pem
   ```
6. Repeat for **Private Instance B**.

### 4. Install a Python Application in Private Instances
1. Create an `index.html` file in both **Private Instances A & B**:
   ```html
   <!DOCTYPE html>
   <html>
   <body>
   <h1>My First AWS Project - Deploying Apps in Subnets</h1>
   </body>
   </html>
   ```
2. Run the Python server inside private instances:
   ```bash
   python3 -m http.server 8000
   ```

### 5. Create a Load Balancer and Attach Target Groups
- **Application Load Balancer (ALB)** handles **HTTP/HTTPS traffic (Layer 7)**.

#### Steps to Create Load Balancer:
1. Create an **Application Load Balancer**.
2. **Scheme**: Internet-facing.
3. **IP Type**: IPv4.
4. **VPC**: Select the project VPC.
5. **Subnets**: Select only the **public subnets**.
6. **Security Group**: Select the VPC security group.
7. **Listeners & Routing**:
   - **Protocol**: HTTP.
   - **Port**: 80.
   - **Target Group**: Port **8000**, add **Private Instances A & B** as targets.
8. If you get an **error stating listener port is unavailable**, update the **security group** to allow HTTP traffic on **port 80**.

![Listener Port Error](https://github.com/user-attachments/assets/d821fd1b-1a62-49b7-8a3c-1a4d5f114d73)

#### Fix: Allow HTTP Traffic in Security Group

![Allow HTTP Traffic](https://github.com/user-attachments/assets/de57af10-df6f-4af8-a37f-6b9c9790ca8c)

### 6. Verify the Application
- Copy the **Load Balancer DNS/IP** and paste it into the browser.
- You should see the **deployed application from the private subnets**.

![Application Output](https://github.com/user-attachments/assets/51439907-c4eb-466c-8285-b2ba79c09a62)


# 
    
