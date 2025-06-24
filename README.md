# AWS_Capstone_Project

# 🌍 Employee Manager – Multi-Region Deployment with Route 53 Failover

## 1. 🧾 Introduction

This project sets up a resilient, production-ready, multi-region web application using a 3-tier architecture. It leverages **Amazon EKS** for container orchestration, **Amazon ECR** for image Storage, **Amazon RDS MySQL** for data persistence, and **Route 53** for intelligent global traffic management and failover.

Infrastructure is provisioned with **CloudFormation in Region 1** and **Terraform in Region 2**, demonstrating best practices for hybrid infrastructure-as-code (IaC) deployments. CI/CD pipelines automate both **frontend (Angular)** and **backend (Spring Boot)** deployments.

---

## 2. 📱 Application Overview

| Component  | Tech Stack                        |
|------------|-----------------------------------|
| Frontend   | Angular (TypeScript)              |
| Backend    | Spring Boot (Java)                |
| Database   | Amazon RDS (MySQL)                |
| Infra      | CloudFormation (us-west-1), Terraform (us-west-2) |
| Orchestration | Amazon EKS                     |

---

## 3. 🛠 Infrastructure Design Principles

| Goal              | Strategy                                                   |
|-------------------|------------------------------------------------------------|
| High Availability | Multi-AZ, Auto-scaling EKS nodes, Route 53 failover        |
| Fault Tolerance   | Redundant infrastructure across two regions                |
| Scalability       | Horizontal scaling via EKS node groups                     |
| DR Readiness      | Active-passive design with Route 53 and ALBs               |
| Automation        | Fully automated CI/CD pipelines for app deployment         |

---

## 4. 🏗️ Overall Architecture

<img src="https://raw.githubusercontent.com/KIREETI1234/AWS_Capstone_Project/main/architecture.png" alt="Architecture Diagram" height="400" width="400"/>




## 5. 🌩 CloudFormation Deployment – Region 1

**📁 File**: `infra/cloudformation/template.yaml`

### 🔍 Key Resources
- **Networking**: VPC, Public/Private Subnets, NAT Gateway, Route Tables
- **Compute**: EKS Cluster, Node Groups (Auto-scaling), IAM roles
- **Database**: RDS MySQL (Multi-AZ) with private access
- **Monitoring**: CloudWatch alarms, SNS topics
- **Security**: IAM roles for least privilege, SGs for EKS/RDS

### 🛠 Deployment Methods
- **Manual**: Create a CloudFormation stack and upload `yaml` file
- **Automated**:
  1. Push template to GitHub
  2. Set up CodePipeline (source: GitHub, deploy: CloudFormation)
  3. Trigger deploy on push

---

## 6. ⚙️ Terraform Deployment – Region 2

**📁 Directory**: `infra/terraform/main.tf`

### 🔧 Modules & Resources
- `vpc`: Creates VPC and subnets
- `eks`: Deploys EKS cluster and node groups
- `rds`: Provisions MySQL DB (standby or DR)

### 🛠 Steps to Deploy
```bash
cd infra/cloudformation/cft.yaml
terraform init
terraform validate
terraform plan
terraform apply
