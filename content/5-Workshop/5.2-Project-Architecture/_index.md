---
title : "Project Architecture"
date :  "2025-11-05" 
weight : 2 
chapter : false
pre : " <b> 5.2 </b> "
---
 
#### Architecture Overview

This document describes the recommended production-ready architecture for ReGenZet — an EV Garage Management System — and explains the technology choices used in the workshop.

- Frontend: A React single-page application deployed and hosted via **AWS Amplify**. Amplify provides automated CI/CD, asset hosting and integration with CloudFront for global caching and fast client delivery.
- Backend: Containerized Spring Boot services packaged as Docker images and deployed to **Amazon ECS (Fargate)**. Fargate removes host management overhead and provides scalable, serverless compute for microservices.
- Database: **Amazon RDS (MySQL/Postgres)** hosted in private subnets. RDS provides automated backups, snapshots, Multi-AZ options and encryption at rest (KMS).
- API Management: **Amazon API Gateway** exposes a single HTTPS entry point for client traffic, handles request routing, TLS termination and request throttling.
- Media Storage: **Amazon S3** holds vehicle inspection images, videos and other media. Use presigned URLs for secure direct-upload/download to offload traffic from the backend.
- Asynchronous / Serverless components:
  - Email pipeline: Backend (Spring) publishes an event to **SNS**, which triggers a **Lambda** to send email via **Amazon SES**.
  - AI/Chat pipeline: Frontend → API Gateway → Lambda → **Amazon Bedrock** (or other managed LLM) for inference and conversational workflows.
- Network & Security: Deploy resources inside a VPC with well-defined Public and Private subnets. Use **Security Groups** and **NACLs** to control traffic. Use **VPC Endpoints** (Gateway and Interface) for S3 and private service access, keeping traffic inside the AWS network.

#### Why this architecture?

Security-first
- The backend and RDS instances reside in private subnets and never expose database ports to the public internet. API Gateway and load-balanced frontends terminate TLS at the edge, while internal services communicate over private networking.

Cost-efficiency
- Fargate and Lambda offer a pay-for-what-you-use model. When appropriate, consider Fargate Spot for non-critical workloads and configure autoscaling and lifecycle policies for S3 to reduce costs.

Operational simplicity & modern patterns
- Clear separation of concerns between frontend and backend, with API Gateway as a single ingress point. Event-driven components (SNS, Lambda) decouple email and AI processing from request-response paths, improving resilience and scalability.

Developer productivity
- AWS Amplify simplifies frontend CI/CD and hosting. Container workflows with Docker + ECR and ECS Fargate enable reproducible deployments for backend services.

Security and best-practice highlights
- Least-privilege IAM roles for services and cross-account access where needed.
- KMS-managed encryption for RDS and S3 objects.
- WAF and rate-limiting on API Gateway to mitigate application-level attacks.

This architecture balances enterprise-grade security with cost-effective serverless patterns and provides a pragmatic path for incremental adoption of advanced features (observability, multi-region DR, Bedrock-powered AI).

![overview](/images/aws_architecture-finish.drawio.png)