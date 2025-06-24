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
```

## 7. Global Traffic Management (Route 53)
 
### 🧠 Failover Logic
 
| Record Type | Region    | Failover Role | Health Check |
| ----------- | --------- | ------------- | ------------ |
| A (Alias)   | us-east-1 | PRIMARY       | Enabled      |
| A (Alias)   | us-west-2 | SECONDARY     | N/A          |
 
> If primary ALB is unhealthy, traffic fails over to secondary.
 
---
 
## 8. CI/CD Pipeline Setup
 
### 📦 Tools Used
 
| Service      | Purpose                                  |
| ------------ | ---------------------------------------- |
| GitHub       | Source code repository                   |
| CodePipeline | orchestrates the CI/CD Workflow          |
| CodeBuild    | Builds Dockerimages & pushes to ECR      |
| Amazon ECR   | Stores Docker Images                     |
| Amazon EKS   | Runs the Application inside the pods     |
| Route53      | DNS Failover for High Availability       |
| Helm         | Kubernetes app deployments               |
 
### 📊 Architecture Diagram
 
```
┌────────────┐       ┌────────────┐       ┌──────────────┐       
│  GitHub    │ ───▶  │ CodeBuild  │ ───▶ │EKS/Kubernetes│ 
└────────────┘       └────────────┘       └──────────────┘       
     ▲                        │                   │                      
     └─────────────[Triggered on push]────────────┴────[Rolling updates] [webhook Trigers]
```
 
### 🧰 Sample `buildspec.yml`
 
```yaml
version: 0.2
phases:
  install:
    runtime-versions:
      java: corretto11
  build:
    commands:
      - mvn clean package
      - kubectl apply -f deployment.yaml
artifacts:
  files: ["**/*"]
```
 
 
### 🛠 CI/CD Setup Steps
 
1. Create CodePipeline with stages: Source (GitHub), Build (CodeBuild), Deploy (CodeBuild or CodeDeploy)
2. Store Docker image in ECR (if containerized)
3. Use IAM roles with EKS/CodeBuild permissions
4. Monitor via CloudWatch Logs for CPU Utilization etc.
 
---
 
## 9. Monitoring & Alerting
 
### 🔔 Alarms
 
| Metric                 | Threshold | Action    |
| ---------------------- | --------- | --------- |
| RDS CPUUtilization     | >75%      | SNS Email |

 
> Alerts delivered via SNS Topic (email notification)
 
---
 
## 10. Security Best Practices
 
* **Private RDS**: Not publicly accessible
* **IAM Roles**: Least privilege principle
* **Ingress Rules**: Restrict traffic using SGs and NACLs
 
---
 
## 11. Future Enhancements
 
* Integrate **Prometheus + Grafana** for observability
* Extend CI/CD with canary/blue-green deployments
 
---
 
## 12. Author
 
**Arjun Koppineni**
GitHub: [@KIREETI1234](https://github.com/kireeti1234)
 
---
 
## 📝 Useful Commands
 
```bash
aws eks update-kubeconfig --region <region> --name <cluster-name>
kubectl get nodes
kubectl get svc
kubectl logs -l app=myapp
```


