---
title : "Introduction"
date :  "2025-11-05" 
weight : 1 
chapter : false
pre : " <b> 5.1 </b> "
---
 
#### ApexEV — Workshop Introduction (Short)

ApexEV is an enterprise-grade EV garage management platform that digitizes workshop operations, improves customer experience, and helps technicians work faster and safer.

Why this workshop:
- Solve common garage problems: manual processes, poor transparency, weak customer care, and data risk.
- Build a secure, scalable and cost-efficient cloud backend from day one using AWS best practices.

Core architecture (summary):
- **Frontend:** React app hosted on **AWS Amplify** (CI/CD + CloudFront).
- **Backend:** Spring Boot services in **ECS (Fargate)** — serverless containers.
- **Database:** **Amazon RDS** (private subnets, automated backups, KMS encryption).
- **Storage:** **Amazon S3** for media (use presigned URLs for direct uploads).
- **API + Async:** **API Gateway** as HTTPS ingress; **SNS → Lambda → SES** for email; **Lambda → Bedrock** for AI/chat.
- **Network & Security:** VPC (public/private), Security Groups, VPC Endpoints, WAF and least-privilege IAM.

Key benefits:
- Security-first: backend and DB remain private; edge services terminate TLS and enforce WAF/rate limits.
- Cost-aware: Fargate + Lambda (pay-per-use), lifecycle rules and autoscaling reduce costs.
- Modern & modular: frontend/backend separation, event-driven async flows for resilience and scale.

Workshop goals:
- Provision network and secure services, deploy frontend + backend, connect RDS and S3, and integrate email and AI pipelines. Each module includes steps, recommended settings and cleanup instructions.
