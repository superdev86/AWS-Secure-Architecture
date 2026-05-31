# AWS-Secure-Architecture

## 📌 Project Overview

This project demonstrates the transformation of a vulnerable e-commerce cloud environment into a secure, production-ready AWS architecture. The initial SecureCart infrastructure prioritized speed over security, resulting in publicly exposed EC2, S3, and RDS resources.

The goal of this project was to identify these risks and rebuild the system using AWS security best practices aligned with the AWS Well-Architected Framework. The infrastructure was built following a guided cloud architecture lab by Tech with Lucy.

---

## 🚨 Phase 1: Insecure Architecture (Baseline)

The original architecture contained several critical security issues:

- EC2 instance was publicly accessible
- RDS database was exposed to the internet
- No proper network segmentation (flat network design)

### Key Risks:
- Unauthorized access to compute and database resources
- Exposure of sensitive application data
- No isolation between public and backend systems

---

## 🔐 Phase 2: Secure Architecture (Final Design)

The architecture was redesigned using a secure VPC with public and private subnet separation.

---

## 🏗️ Architecture Overview

<img width="1095" height="613" alt="Screenshot 2026-05-31 002044" src="https://github.com/user-attachments/assets/9e515502-9aea-4a5b-9ea0-08a134480461" />

---

## 🌐 VPC Design

### Public Subnet contains:
- Application Load Balancer (internet-facing)
- Bastion EC2 (secure SSH entry point)
- NAT Gateway (enables outbound internet access for private subnet)

### Private Subnet contains:
- EC2 application server (backend logic)
- RDS MySQL database (fully isolated, no public access)

---

## ⚙️ Core AWS Services Used

- Application Load Balancer 
- EC2 (Bastion + Application server)
- Amazon RDS (MySQL)
- NAT Gateway + Internet Gateway
- VPC (custom networking with public/private subnets)
- Security Groups (network-level access control)

---

## 🔐 Security Implementation

- EC2 instance moved to private subnet (no direct public access)
- Bastion host used for controlled SSH access into private network
- RDS database restricted to application security group only
- NAT Gateway used for secure outbound traffic from private subnet
- Security groups implemented as primary traffic control layer

---

## 🐞 Key Issues & Troubleshooting

### 1. SSH Key Pair Misconfiguration
A private EC2 instance was initially launched without a key pair and I thought this is why it was resulting in “Permission denied (publickey)” errors. The instance was recreated with the correct key pair, but that didn't restore SSH access via bastion host. I had to save my key pair .pem file inside the bastion EC2 then SSH into the private instance to establish the connection.

---

### 2. ALB 503 Error
The Application Load Balancer returned a 503 error due to a target group still referencing a deleted EC2 instance. Updating the target group to the correct instance resolved the issue and restored application access.
<img width="1652" height="272" alt="Screenshot 2026-05-30 222742" src="https://github.com/user-attachments/assets/0e7321d2-700b-4dd9-91de-7bff37f4d546" />

<img width="747" height="257" alt="Screenshot 2026-05-30 223032" src="https://github.com/user-attachments/assets/96f00a99-6ce5-4a27-a234-0008b31d3657" />


---

## 🔄 Future Improvements

- Replace Bastion Host with AWS Systems Manager Session Manager to remove SSH dependency and improve security
- Add AWS WAF to protect against application-layer attacks (SQL injection, XSS, bot traffic)
- Enable RDS Multi-AZ for high availability and automatic failover
- Implement Auto Scaling policies for EC2 based
- Add VPC endpoints to allow private subnet access to AWS services without using a NAT Gateway

---

## 📘 Key Learnings

- Proper VPC segmentation is critical for securing multi-tier applications
- Security Groups act as the main traffic control layer between application tiers
- ALB requires healthy target group instances to serve traffic correctly
- Debugging architectures requires systematically validating networking, security groups, routing, and application health
