# PrestaShop Deployment on AWS Free Tier

## Project Overview
This repository documents the deployment of [PrestaShop](https://www.prestashop.com/en), an open-source e-commerce platform, on Amazon Web Services (AWS) using only Free Tier resources. The setup adheres to best practices for separation of concerns by hosting the application on an EC2 instance and the database on a separate RDS instance. This project was completed as part of a cybersecurity intern recruitment task, emphasizing secure cloud architecture, cost optimization, and detailed documentation.

Key highlights:
- **Publicly Accessible URL**: [http://ec2-xx-xx-xx-xx.compute-1.amazonaws.com](http://ec2-xx-xx-xx-xx.compute-1.amazonaws.com) (Replace with your actual EC2 public DNS or Elastic IP)
- **Compliance with Requirements**: Database hosted separately; all on AWS Free Tier (t3.micro EC2, db.t4g.micro RDS).
- **Cybersecurity Focus**: Implemented security groups for least-privilege access, no public DB exposure, and basic hardening.
- **Tools & Technologies**: AWS EC2, RDS (MySQL), Apache/PHP (LAMP stack), SSH for secure access.

This deployment demonstrates proficiency in cloud infrastructure, DevOps practices, and security engineering—ideal for showcasing in portfolios or interviews.

## Architecture Design
The architecture follows a multi-tier design to enhance security, scalability, and maintainability:
- **Presentation/Application Tier**: EC2 instance running the PrestaShop PHP application on Apache.
- **Data Tier**: RDS MySQL instance for database storage, isolated from the application server.
- **Network Security**: Custom security groups restrict traffic—SSH/HTTP to EC2 only from authorized sources, and DB access limited to the EC2 instance.
- **Connectivity**: EC2 connects to RDS via private endpoint; public access via EC2's public IP/DNS.
- **Cost Management**: All components within AWS Free Tier limits (750 hours/month for instances, 20 GB storage).

### High-Level Architecture Diagram
Below is a Mermaid diagram for visual representation (renders automatically on GitHub). For a more detailed version, see `/diagrams/architecture-diagram.png` (export from tools like draw.io or Lucidchart).

### Rationale

- **Separation of Tiers:**  
  Reduces attack surface; compromising the application server does not directly expose the database.

- **Least Privilege:**  
  Security Groups act as firewalls. For example, RDS inbound traffic is allowed **only** from the EC2 Security Group.

- **Free Tier Optimization:**  
  - EC2: `t3.micro` (2 vCPU, 1 GB RAM)  
  - RDS: `db.t4g.micro`  
  - Minimal storage to stay within Free Tier limits

- **Scalability Potential:**  
  Architecture can easily support Auto Scaling Groups or Load Balancers in a production environment.

- **Security Enhancements:**  
  - No root SSH access  
  - Key-based authentication  
  - Installation folder removed post-setup to prevent exploitation

---

### Implementation Steps

Below is a step-by-step guide to replicating this deployment.  
All steps were performed on **January 16, 2026**, using the **AWS Console** in the **eu-west-1** region (selected for low latency from Port Harcourt, Nigeria).

---

### Step 1: AWS Account Setup

- Sign up or log in to **AWS Free Tier**
- Select region: **eu-west-1**

**Artifacts:**  
- AWS Console Dashboard

---

### Step 2: Configure Security Groups

- **PrestaShop-EC2-SG**
  - Inbound SSH (My IP only)
  - HTTP (80) and HTTPS (443) from Anywhere

- **PrestaShop-RDS-SG**
  - Inbound MySQL (3306) allowed **only** from `PrestaShop-EC2-SG`

**Artifacts:**  
- EC2 Security Group  
- RDS Security Group  

**Decision:**  
Restricts database access to the application server only, aligning with **zero-trust security principles**.

---

### Step 3: Launch RDS Database

- Engine: **MySQL (Free Tier)**
- Instance type: `db.t4g.micro`
- Storage: 20 GB SSD
- Endpoint: `prestashop-db.xxxxx.rds.amazonaws.com` (private, no public access)
- Database name: `prestashop`
- Username: `admin`

**Artifacts:**  
- RDS Creation Wizard  
- RDS Instance Details  

**Decision:**  
Single-AZ deployment for cost efficiency; password-based authentication with strong credentials.

---

### Step 4: Launch EC2 Instance

- AMI: **Amazon Linux 2023**
- Instance type: `t3.micro`
- Key pair: New PEM file for SSH access
- Public IP: Enabled

**Artifacts:**  
- EC2 Launch Wizard  
- EC2 Instance Details  

**Decision:**  
Amazon Linux provides a lightweight and secure base image; public IP enables external access for the web application.

---

### Step 5: Install PrestaShop on EC2

- SSH into instance:
  ```bash
  ssh -i key.pem ec2-user@public-dns
