---
title: "Proposal"
date: "2025-11-05"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# APEX-EV Electric Vehicle Service Platform 

### 1. Executive Summary
ApexEV is a comprehensive management platform designed to digitize and optimize the maintenance process at electric vehicle service centers. The system centralizes the entire service lifecycle—from appointment scheduling, vehicle reception, and repairs to payment—eliminating manual operations and enhancing operational efficiency. By leveraging the power of AWS's Hybrid architecture, combining the flexibility of containers on Amazon ECS Fargate and serverless services, ApexEV provides real-time monitoring, high security, and increased customer satisfaction. The platform ensures secure, role-based access for five key user roles through Amazon Cognito.

### 2. Problem Statement
### What’s the Problem?
The current operational process relies heavily on manual methods, leading to inefficient work performance, fragmented data, and a passive customer experience.

### The Solution
The platform utilizes a multi-layered security architecture. The React.js frontend is deployed on AWS Amplify and distributed globally via Amazon CloudFront, protected by AWS WAF (Web Application Firewall).

The entire core business logic (Backend) is a Spring Boot application running on Amazon ECS Fargate, placed securely within a Private Subnet of a VPC (Virtual Private Cloud). Amazon API Gateway acts as the entry point, receiving requests from the frontend (which are authenticated by Amazon Cognito) and forwarding them to an Application Load Balancer (ALB) before reaching ECS.

To achieve high performance, the system uses Amazon ElastiCache (Redis) for caching and Amazon RDS (SQL Server) as the primary relational database. Multimedia data (images/videos) is stored on Amazon S3.

Asynchronous tasks (such as sending emails) are processed through Amazon SNS -> AWS Lambda -> Amazon SES. Outbound internet connections from the backend (e.g., calling payment APIs, calling the Gemini API) are securely handled through a NAT Gateway.

### Benefits and Return on Investment
The architectural solution on AWS not only digitizes the entire workflow but also creates a significant competitive advantage in performance, security, and scalability. The platform ensures an instant and seamless user experience thanks to the global content delivery network of CloudFront and ElastiCache caching, while data is securely protected through multiple layers with AWS WAF and the VPC private network.

Operating on Amazon ECS Fargate automates processes and eliminates the burden of infrastructure management, allowing staff to focus fully on their expertise and customer service. With an estimated monthly operating cost of $50 - $85 USD, this investment is quickly offset by minimizing security risks, increasing labor productivity, and improving revenue through customer satisfaction. Consequently, the return on investment (ROI) is projected to be between 8-15 months, after which the platform will become a sustainable growth driver for the business.

### 3. Solution Architecture
The APEX-EV electric vehicle maintenance management platform implements a multi-layered, high-performance security architecture on AWS. The user interface is built with React.js, deployed via AWS Amplify, and distributed globally by Amazon CloudFront, while being protected by the AWS Web Application Firewall (WAF). The entire core business logic is a Spring Boot application running on Amazon ECS Fargate, placed securely within a Private Subnet. Amazon API Gateway acts as the communication gateway, authenticating requests via Amazon Cognito before forwarding them to an Application Load Balancer and then to the application. To optimize speed, the platform uses Amazon ElastiCache (Redis) for caching, Amazon RDS (SQL Server) for the primary database, and Amazon S3 for storing multimedia files. Asynchronous tasks, such as sending notifications, are processed efficiently through the Amazon SNS, Lambda, and SES service chain.

![APEX-EV Platform Architecture](/images/2-Proposal/APEX-EV.jpeg)

### AWS Services Used
- *Amazon ECS Fargate*: Runs the backend application (Spring Boot) as a container without needing to manage servers.
- *AWS Lambda*: Handles asynchronous tasks, for example, processing notifications from SNS to send emails.
- *Amazon API Gateway*: Communicates with the web application.
- *Amazon S3*: Stores raw data (data lake) and processed data (2 buckets).
- *Amazon RDS*: Provides a relational database (SQL Server) to store structured data (profiles, appointments, spare parts).
- *AWS Amplify*: Hosts the Next.js web interface.
- *Amazon Cognito*: Manages user access and permissions.
- *Amazon ElastiCache*: Provides an in-memory cache (Redis) to speed up data retrieval and improve performance.
- *NAT Gateway*: Allows resources in a private subnet to connect to the internet securely.
- *AWS WAF*: Web application firewall, protects the platform from common web attacks.
- *AWS SNS*: Notification service, used to initiate asynchronous processes (e.g., sending a notification to trigger Lambda).
- *Route 53*: DNS service, responsible for routing your domain name to the application.
- *AWS SES*: Email sending service, handles sending notifications and quotes to customers.
- *AWS ECR*: Stores the application's container images (Docker) for deployment on ECS Fargate.
- *AWS CloudFront*: Content Delivery Network (CDN) that helps accelerate the loading and distribution of the web interface (frontend) to users globally with low latency.

### Component Design
- *Request Reception*: Amazon API Gateway acts as the entry point, receiving and routing all requests from the web interface after they have been authenticated.
- *Data Storage*: Relational data (Profiles, Appointments, Spare Parts) is stored on Amazon RDS; multimedia files (maintenance photos/videos) are stored on Amazon S3.
- *Business Processing*: All core logic is handled by the Spring Boot application running on Amazon ECS Fargate; asynchronous tasks like sending emails are processed by AWS Lambda.
- *Web Interface*: AWS Amplify deploys and hosts the React.js application. Amazon CloudFront distributes the content globally to increase speed and is protected by AWS WAF.
- *User Management*: Amazon Cognito manages the entire process of authentication, detailed authorization, and account security for all users.

### 4. Technical Implementation
**Implementation Phases**
This project has two parts—setting up weather edge stations and building the weather platform—each following 4 phases:
1. *Research and Architecting*: Research suitable technologies (React.js, Spring Boot) and design a detailed multi-layer system architecture on AWS (1 month before starting).
2. *Cost Calculation and Feasibility Check*: Use the AWS Pricing Calculator to estimate operating costs for core services like ECS, RDS, and WAF, and determine a feasible plan.
3. *Architectural Adjustments for Cost/Solution Optimization*: Refine the architecture, selecting appropriate configurations for ECS Fargate, RDS, and ElastiCache to balance performance and cost.
4. *Development, Testing, Deployment*: Code the React.js (Frontend) and Spring Boot (Backend) applications, deploy the infrastructure using AWS CDK, then conduct comprehensive testing and go live.

**Technical Requirements**
- *User Interface (Frontend)*: Practical knowledge of React.js to build a modern user interface. Use AWS Amplify to automate the deployment process, Amazon CloudFront for low-latency global content delivery, and AWS WAF to protect the application from web attacks.
- *Core System (Backend & Infrastructure)*: In-depth knowledge of Java/Spring Boot to develop business logic. The application is containerized using Docker, stored on Amazon ECR, and runs on Amazon ECS Fargate. Requires understanding of Amazon RDS (SQL Server) for the primary database and ElastiCache (Redis) for caching. Proficient use of AWS CDK to define and deploy the entire infrastructure automatically, including VPC, API Gateway, Application Load Balancer, and security rules. Manage authentication and detailed user authorization through Amazon Cognito.

### 5. Timeline & Milestones
**Project Timeline**
1. *Phase 1 (Weeks 1-2)*: Design and Foundation:
- Analyze & design the detailed AWS architecture. Design the database schema and API endpoints.
- Configure the AWS environment (VPC, Subnets, IAM, Cognito).
- Set up a basic CI/CD pipeline (GitLab -> Amplify, GitLab -> ECR).
2. *Phase 2 (Weeks 3-4)*: Core Service Flow Development:
- Develop the Customer flow (Booking appointments, Personal profile).
- Develop the Service Advisor flow (Managing appointments, Creating quotes).
- Develop the Technician flow (Maintenance checklist, Updating progress).
3. *Phase 3 (Weeks 5-6)*: Admin & Advanced Features Development:
- Build the Management Module (Viewing reports, Inventory management, HR).
- Build the Admin Module (Permissions, Service management).
- Integrate the AI Chatbot (calling the Gemini API via NAT Gateway) and the notification flow (SNS/SES).
4. *Phase 4 (Weeks 7-8)*: Testing, Optimization, and Go-Live:
- Conduct internal User Acceptance Testing (UAT) to find bugs and gather feedback.
- Focus on bug fixing and making refinements based on feedback.
- Optimize security (WAF Rules, IAM policies) and configure monitoring (CloudWatch Dashboards/Alarms).
- Official deployment (Go-live).

### 6. Budget Estimation
**Infrastructure Costs**
- AWS Lambda: $0.00 USD/month (1,000 requests, 512 MB storage).
- S3 Standard: $0.15 USD/month (6 GB, 2,100 requests, 1 GB scan).
- Amazon RDS: $0.00 USD/month (Free for 750 hours/month for 12 months).
- AWS Amplify: $0.35 USD/month (256 MB, 500 ms requests).
- Amazon API Gateway: $0.01 USD/month (2,000 requests).
- Amazon ElastiCache: $0.00 USD/month (Free for 750 hours/month for 12 months).
- Amazon ECS Fargate: ~$10.00 USD/month for 1 task (0.5 vCPU, 1GB RAM).
- Application Load Balancer: ~$16.20 USD/month.
- NAT Gateway: ~$32.45 USD/month (Internet egress for ECS/AI).
- AWS WAF: ~$6.00 USD/month (Firewall).
- Serverless Services: ~$5.00 USD/month, almost entirely free within the Free Tier (except for Route 53 and S3 if exceeding 5GB).
*Total*: $~69.95 USD/month.

### 7. Risk Assessment
#### Risk Matrix
- System downtime: High impact, low probability.
- Security breach/data loss: High impact, medium probability.
- Exceeding operational costs: Medium impact, medium probability.
#### Mitigation Strategies
- System: Deploy infrastructure across multiple Availability Zones (Multi-AZ) for RDS and ECS Fargate. Use an Application Load Balancer to automatically distribute traffic and handle failover.
- Security: Use AWS WAF to filter malicious requests. Enforce strict permissions with Amazon Cognito and apply the principle of least privilege. The backend is placed within a private network (Private Subnet).
- Cost: Use AWS Budgets to set up alerts when costs exceed thresholds. Regularly monitor and optimize resources (right-sizing) to avoid waste.
#### Contingency Plans
- Activate the incident response procedure, using RDS backups to restore data.
- Deploy a static maintenance notice page on S3/CloudFront to inform users in the event of a prolonged system outage.

### 8. Expected Outcomes
#### Technical Improvements: 
A centralized, API-first system that completely replaces disjointed manual processes. The architecture is secure and scalable (thanks to ECS and RDS). Data is centrally managed, secure, and ready for analysis.
#### Long-term Value
Builds a valuable data asset, forming the basis for future business analysis and strategic decisions. Creates a solid technological foundation for easily expanding new features (e.g., AI-powered predictive failure analysis). Increases employee work efficiency and boosts customer retention rates through a transparent and superior service experience.