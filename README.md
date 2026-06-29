# aws-highly-available-web-app
Built a highly available web application on AWS using a custom VPC, EC2 Auto Scaling Group, Application Load Balancer, Bastion Host, NAT Gateway, and private/public subnets across multiple Availability Zones.
# AWS Highly Available Web Application using Auto Scaling & Application Load Balancer

## 📖 Overview

This project demonstrates the deployment of a highly available web application on AWS using a custom VPC architecture. The application runs on EC2 instances in private subnets, while an Application Load Balancer (ALB) distributes incoming traffic. Auto Scaling ensures high availability by automatically replacing unhealthy instances and scaling based on demand. A Bastion Host is used to securely access the private EC2 instances.

---

## 🏗️ Architecture

```text
                    Internet
                        │
                Internet Gateway
                        │
        ┌──────────────────────────┐
        │ Application Load Balancer│
        │      (Public Subnets)    │
        └─────────────┬────────────┘
                      │
                Target Group
                HTTP : 8000
                      │
        ┌─────────────┴─────────────┐
        │                           │
  EC2 Instance                 EC2 Instance
 Private Subnet               Private Subnet
     AZ-1                        AZ-2
        │                           │
 Python HTTP Server          Python HTTP Server
      Port 8000                 Port 8000
        │                           │
        └────── Auto Scaling Group ──────┘

                ▲
                │ SSH
        Bastion Host (Public Subnet)
```

---

## ☁️ AWS Services Used

- Amazon VPC
- Public & Private Subnets
- Internet Gateway
- NAT Gateway
- Route Tables
- EC2
- Launch Template
- Auto Scaling Group
- Application Load Balancer (ALB)
- Target Group
- Bastion Host
- Security Groups

---

# Step 1: Create VPC

Create a VPC using **VPC and More**.

### Configuration

- Project Name
- 2 Availability Zones
- 2 Public Subnets
- 2 Private Subnets
- 1 NAT Gateway per Availability Zone
- No VPC Endpoints

---

# Step 2: Create Launch Template

Launch Template Configuration

- Ubuntu Server
- Instance Type
- Key Pair
- Security Group

### Security Group

Inbound Rules

| Type | Port |
|------|------|
| SSH | 22 |
| Custom TCP | 8000 |

---

# Step 3: Create Auto Scaling Group

Configuration

- Launch Template
- Private Subnets (AZ-1 & AZ-2)

Capacity

- Desired Capacity : 2
- Minimum Capacity : 1
- Maximum Capacity : 4

---

# Step 4: Create Bastion Host

Launch one EC2 instance in the Public Subnet.

Security Group

| Type | Port |
|------|------|
| SSH | 22 |

Enable **Auto Assign Public IP**.

---

## Copy PEM File to Bastion

```bash
scp -i malla.pem malla.pem ubuntu@<Bastion-Public-IP>:/home/ubuntu/
```

SSH into Bastion

```bash
ssh -i malla.pem ubuntu@<Bastion-Public-IP>
```

Give Permission

```bash
chmod 600 malla.pem
```

SSH into Private EC2

```bash
ssh -i malla.pem ubuntu@<Private-IP>
```

---

# Step 5: Deploy Web Application

Create a directory

```bash
mkdir website
```

Move into directory

```bash
cd website
```

Create HTML file

```bash
vi index.html
```

Paste the following code

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

Save and Exit

```
:wq
```

Run Python HTTP Server

```bash
python3 -m http.server 8000
```

Verify

```bash
curl localhost:8000
```

---

# Step 6: Create Application Load Balancer

Type

- Internet Facing

Network Mapping

- Public Subnet AZ-1
- Public Subnet AZ-2

Security Group

Allow

| Type | Port |
|------|------|
| HTTP | 80 |
| Custom TCP | 8000 |

---

# Step 7: Create Target Group

Configuration

- Target Type : Instance
- Protocol : HTTP
- Port : 8000

Register Targets

- Private EC2 Instance (AZ-1)
- Private EC2 Instance (AZ-2)

Wait until both targets become **Healthy**.

---

# Step 8: Configure Listener

Listener

```
HTTP : 80
```

Forward requests to

```
Target Group (Port 8000)
```

---

# Step 9: Test the Application

Copy the DNS name of the Load Balancer.

Example

```text
http://ff-lb-xxxxxxxx.ap-south-1.elb.amazonaws.com
```

Expected Output

```
My First Heading

My second paragraph.
```

---

## 🔐 Security Groups

### Bastion Host

| Protocol | Port | Source |
|----------|------|--------|
| SSH | 22 | My IP |

---

### Load Balancer

| Protocol | Port | Source |
|----------|------|--------|
| HTTP | 80 | 0.0.0.0/0 |
| Custom TCP | 8000 | 0.0.0.0/0 |

---

### Private EC2

| Protocol | Port | Source |
|----------|------|--------|
| SSH | 22 | Bastion Security Group |
| Custom TCP | 8000 | Load Balancer Security Group |

---

## 📁 Project Structure

```text
AWS-Highly-Available-Web-App/
│
├── README.md
├── index.html
└── architecture.png
```

---

## 🚀 Features

- Custom VPC
- Public & Private Subnets
- Bastion Host
- NAT Gateway
- Launch Template
- Auto Scaling Group
- Application Load Balancer
- Target Group
- High Availability
- Fault Tolerance
- Secure SSH Access
- Python HTTP Server

---

## 📚 Learning Outcomes

- VPC Design
- Route Tables
- Internet Gateway
- NAT Gateway
- Security Groups
- Bastion Host
- Launch Templates
- Auto Scaling
- Application Load Balancer
- Target Groups
- High Availability Architecture
- Hosting Static Websites on EC2

---

## 👨‍💻 Author

**Furqan Mulla**

- GitHub: https://github.com/mullafurqan
- LinkedIn: https://www.linkedin.com/in/furqan-mulla
