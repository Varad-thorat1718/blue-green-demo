# Production-Ready Blue-Green Deployment using Jenkins and Application Load Balancer

## Project Overview

This project demonstrates a **production-ready Blue-Green deployment strategy** using Jenkins and AWS.
The goal is to achieve **zero-downtime deployments** by maintaining two identical environments (**Blue and Green**) and switching production traffic only after successful validation.

The Jenkins pipeline deploys the new application version to the inactive environment, performs a health check, and then switches traffic using an Application Load Balancer. If the deployment fails, the system automatically rolls back to the previous stable environment.

---

## Problem Statement

In traditional deployments, updating the production server directly can cause:

* Application downtime
* Failed deployments impacting users
* Difficulty rolling back to previous versions

To solve this, we implemented a **Blue-Green deployment model** that ensures uninterrupted service.

---

## Architecture

```
Developer → GitHub → Jenkins Pipeline → Deploy to Inactive Environment
         → Health Check → Switch Traffic via Application Load Balancer
         → Users access updated version
```

Two environments are maintained:

Blue Environment – Current Production Version
Green Environment – New Deployment Version

Only one environment serves live traffic at a time.

---

## Technologies Used

* AWS EC2
* AWS Application Load Balancer
* Jenkins
* GitHub
* AWS CLI
* Linux (Ubuntu)
* Nginx / Apache Web Server

---

## Infrastructure Setup

### EC2 Instances

Two EC2 instances are created:

* Blue Server (Production)
* Green Server (Deployment)

Each instance runs a web server hosting the application.

Example application versions:

```
Version 1 → Blue Server
Version 2 → Green Server
```

---

## Load Balancer Configuration

An **Application Load Balancer (ALB)** is created to manage traffic.

Target Groups:

```
Blue Target Group
Green Target Group
```

The ALB listener forwards traffic to one target group at a time.

---

## Jenkins Pipeline Workflow

The Jenkins pipeline performs the following steps:

1. Pull code from GitHub repository
2. Identify inactive environment
3. Deploy new version to inactive environment
4. Run health check on deployed server
5. Switch traffic to new environment
6. Rollback if health check fails

---

## Pipeline Stages

```
Stage 1: Clone Code
Stage 2: Check Files
Stage 3: Deploy to Green Server
Stage 4: Health Check
Stage 5: Switch Traffic
Stage 6: Rollback (if failure)
```

---

## Health Check

After deployment, Jenkins validates the new environment using:

```
curl http://<server-ip>
```

Expected response:

```
HTTP 200 OK
```

If the response fails, the pipeline stops and traffic remains on the current environment.

---

## Traffic Switching

Traffic switching is done using AWS CLI:

```
aws elbv2 modify-listener \
--listener-arn <listener-arn> \
--default-actions Type=forward,TargetGroupArn=<target-group-arn>
```

This switches user traffic from Blue to Green environment.

---

## Rollback Strategy

If deployment validation fails:

* Traffic remains on the current production environment
* Jenkins marks deployment as failed
* Previous version continues serving users

This ensures **zero downtime and safe deployments**.

---

## Project Structure

```
blue-green-deployment
│
├── Jenkinsfile
├── index.html
└── README.md
```

---

## Deployment Flow

```
Developer
   │
   ▼
GitHub Repository
   │
   ▼
Jenkins CI/CD Pipeline
   │
   ▼
Deploy to Green Environment
   │
   ▼
Health Check
   │
   ▼
Switch Traffic using ALB
   │
   ▼
Users Access Updated Application
```

---

## Key Benefits

* Zero downtime deployments
* Easy rollback mechanism
* Safe production releases
* Automated CI/CD pipeline
* Improved deployment reliability

---

## Future Improvements

* Add Docker containerization
* Implement Kubernetes deployment
* Add monitoring with CloudWatch
* Implement automated scaling

---

## Author

Varad Ankush Thorat
DevOps Enthusiast | AWS | CI/CD | Cloud Automation

---
