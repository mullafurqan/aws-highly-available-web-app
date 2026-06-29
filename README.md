# AWS Highly Available Web Application using Auto Scaling & Application Load Balancer

## 📌 Project Overview

This project demonstrates how to deploy a **Highly Available Web Application** on AWS using a custom VPC architecture. The application is hosted on EC2 instances running in **private subnets**, while an **Application Load Balancer (ALB)** distributes incoming traffic across multiple Availability Zones. A **Bastion Host** provides secure SSH access to the private EC2 instances, and an **Auto Scaling Group** ensures high availability and fault tolerance.

---

## 🏗️ Architecture Diagram

<p align="center">
## 🏗️ Architecture Diagram

![Architecture Diagram](architecture-diagram.png)
</p>

---

## 🌐 VPC Resource Map

The project uses a custom VPC with:

- 2 Availability Zones
- 2 Public Subnets
- 2 Private Subnets
- Internet Gateway
- NAT Gateways
- Route Tables

<p align="center">
![VPC Resource Map](vpc-resource-map.png)
</p>

---

# 🚀 AWS Services Used

- Amazon VPC
- Public & Private Subnets
- Internet Gateway
- NAT Gateway
- Route Tables
- Security Groups
- EC2
- Bastion Host
- Launch Template
- Auto Scaling Group
- Application Load Balancer
- Target Group

---

# 📐 Architecture

```text
                    Internet
                        │
                Internet Gateway
                        │
        ┌──────────────────────────┐
        │ Application Load Balancer│
        │        (Public)          │
        └─────────────┬────────────┘
                      │
               Target Group (8000)
                      │
      ┌───────────────┴───────────────┐
      │                               │
 EC2 Instance 1                  EC2 Instance 2
 Private Subnet                  Private Subnet
      │                               │
 Python HTTP Server             Python HTTP Server
      │                               │
      └──────── Auto Scaling Group ────────┘

                ▲
                │ SSH
          Bastion Host
         (Public Subnet)
```

---

# 📋 Project Workflow

## Step 1: Create VPC

Create a VPC using **VPC and More**

Configuration

- 2 Availability Zones
- 2 Public Subnets
- 2 Private Subnets
- 1 NAT Gateway per Availability Zone
- Internet Gateway
- Route Tables

---

## Step 2: Create Launch Template

Create a Launch Template with

- Ubuntu EC2
- Key Pair
- Security Group

Security Group

| Rule | Port |
|------|------|
| SSH | 22 |
| Custom TCP | 8000 |

---

## Step 3: Create Auto Scaling Group

Configuration

- Desired Capacity = 2
- Minimum Capacity = 1
- Maximum Capacity = 4

Choose

- Launch Template
- VPC
- Private Subnets

---

## Step 4: Create Bastion Host

Launch one Ubuntu EC2 inside Public Subnet.

Enable

- Auto Assign Public IP

Security Group

| Rule | Port |
|------|------|
| SSH | 22 |

---

## Copy PEM file

```bash
scp -i malla.pem malla.pem ubuntu@<bastion-public-ip>:/home/ubuntu/
```

SSH into Bastion

```bash
ssh -i malla.pem ubuntu@<bastion-public-ip>
```

Give permission

```bash
chmod 600 malla.pem
```

SSH into Private EC2

```bash
ssh -i malla.pem ubuntu@<private-ip>
```

---

## Step 5: Deploy Web Application

Create HTML file

```bash
mkdir website

cd website

vi index.html
```

Paste

```html
<!DOCTYPE html>
<html>
<head>
<title>AWS Project</title>
</head>

<body>

<h1>My First Heading</h1>

<p>My second paragraph.</p>

</body>

</html>
```

Start Python HTTP Server

```bash
python3 -m http.server 8000
```

Verify

```bash
curl localhost:8000
```

---

## Step 6: Create Application Load Balancer

Create

- Internet Facing ALB

Choose

- Public Subnet 1
- Public Subnet 2

Security Group

| Rule | Port |
|------|------|
| HTTP | 80 |
| Custom TCP | 8000 |

---

## Step 7: Create Target Group

Configuration

- Target Type = Instance
- Protocol = HTTP
- Port = 8000

Register

- Private EC2 Instance 1
- Private EC2 Instance 2

Wait until both become **Healthy**.

---

## Step 8: Configure Listener

Listener

```
HTTP : 80
```

Forward traffic to

```
Target Group : Port 8000
```

---

## Step 9: Test Application

Copy the DNS name of the Load Balancer

```
http://<load-balancer-dns>
```

Output

```
My First Heading

My second paragraph.
```

---

# 🔒 Security Groups

## Bastion Host

| Protocol | Port | Source |
|----------|------|--------|
| SSH | 22 | My IP |

---

## Load Balancer

| Protocol | Port | Source |
|----------|------|--------|
| HTTP | 80 | 0.0.0.0/0 |
| Custom TCP | 8000 | 0.0.0.0/0 |

---

## Private EC2

| Protocol | Port | Source |
|----------|------|--------|
| SSH | 22 | Bastion Security Group |
| Custom TCP | 8000 | Load Balancer Security Group |

---

# 📁 Project Structure

```text
aws-highly-available-web-app
│
├── README.md
├── index.html
│
└── images
    ├── architecture-diagram.png
    └── vpc-resource-map.png
```

---

# 🎯 Key Features

- Custom VPC
- Public & Private Subnets
- NAT Gateway
- Bastion Host
- Auto Scaling Group
- Launch Template
- Application Load Balancer
- Target Group
- High Availability
- Fault Tolerance
- Secure SSH Access

---

# 📚 Learning Outcomes

- AWS VPC
- Route Tables
- Internet Gateway
- NAT Gateway
- EC2
- Launch Templates
- Auto Scaling
- Bastion Host
- Application Load Balancer
- Target Groups
- High Availability Architecture

---

# 👨‍💻 Author

**Furqan Mulla**

- GitHub: https://github.com/mullafurqan
- LinkedIn: https://www.linkedin.com/in/furqan-mulla
