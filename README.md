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

```mermaid
graph TD
    A[User Browser] -->|HTTP/HTTPS| B[EC2 Instance: PrestaShop App]
    B -->|MySQL Port 3306| C[RDS Instance: MySQL Database]
    D[Security Group: EC2-SG] --> B
    E[Security Group: RDS-SG] --> C
    F[AWS VPC] --> B
    F --> C
    subgraph "Public Access"
        A
        B
    end
    subgraph "Private Access"
        C
    end
    style B fill:#f9f,stroke:#333,stroke-width:2px
    style C fill:#bbf,stroke:#333,stroke-width:2px
