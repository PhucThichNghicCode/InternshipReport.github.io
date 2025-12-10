---
title: "Workshop"
date: "2025-11-05"
weight: 5
chapter: false
pre: " <b> 5 </b> "
---

# Deploy ReGenZet Management System To AWS

#### Overview

ReGenZet is an enterprise-grade EV garage management platform. The objective of this workshop is to design and deploy a secure, cost-optimized, and highly automated cloud infrastructure on AWS to host ApexEV's frontend, backend, storage, and serverless AI/ML functions.

Key architectural principles:
- **Security-first:** least-privilege IAM, encrypted data at rest and in transit, network isolation and controlled service endpoints.
- **Cost optimization:** use managed services with pay-as-you-go models, right-sizing, and automated lifecycle policies for storage and compute.
- **Automation & Observability:** Infrastructure-as-Code, CI/CD pipelines, centralized logging, and automated monitoring/alarms.

Core services used in this workshop:
- **AWS ECS (Fargate)** — run backend microservices without managing servers.
- **AWS Amplify** — host the frontend, provide CI/CD for web clients and manage hosting.
- **Amazon RDS** — managed relational database for transactional data.
- **Amazon S3** — object storage for media, backups, and static assets.
- **AWS Lambda** — serverless functions for AI/ML processing pipelines, notifications and background tasks.

This workshop contains hands-on modules covering the end-to-end stack and best practices for each layer.

#### Content

1. [Workshop Overview](5.1-Workshop-overview)
2. [Project Architecture](5.2-Project-Architecture/)
3. [VPC Of Project](5.3-VPC/)
4. [Database And Storage](5.4-DatabaseAndStorage/)
5. [Compute And Container](5.5-ComputeAndContainer/)
6. [Create Load Balancing](5.6-CreateALB/)
7. [Create Amplify And API Gateway](5.7-AmplifyAndAPIGateway/)
8. [Intruct Deploy Code Frontend and Backend](5.8-Instruct-deploy-be-fe/)