---
title: "Proposal"
date: "2025-11-05"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# APEX-EV Electric Vehicle Service Platform 

### 1. Executive Summary
ReGenZ is a comprehensive management platform designed to digitize and optimize maintenance workflows at service centers. The system centrally manages the entire service lifecycle—from request intake and repair processing to customer care—helping to eliminate manual tasks and enhance efficiency. Leveraging the power of the AWS cloud, ReGenZ combines flexible container architecture on Amazon ECS Fargate with the intelligent processing capabilities of Generative AI through Amazon Bedrock. The solution integrates automated development processes (CI/CD) from GitLab, ensuring rapid deployment speeds, high security, and rigorous monitoring, delivering a superior experience for end-users.

### 2. Problem Statement
### What’s the Problem?
Current operational processes rely heavily on manual methods, leading to inefficiencies, fragmented data, and a lack of intelligent support tools for automated customer interaction.

### The Solution
The platform employs a modern architecture, starting at the Edge layer with Amazon Route 53 for user routing. The interface (Frontend) is hosted on AWS Amplify Hosting, ensuring fast and stable access.
Amazon API Gateway acts as the central hub, intelligently routing requests.

To ensure security, critical components such as ECS Fargate and the Amazon RDS database are placed in a Private Subnet, completely isolated from the public internet. Image data is stored on Amazon S3 and accessed securely via S3 Endpoints. Additionally, the software development process is fully automated: source code from GitLab is packaged and pushed to Amazon ECR for deployment to ECS.

### Benefits and Return on Investment
Adopting this architecture delivers a significant competitive advantage by integrating Artificial Intelligence (GenAI) via Amazon Bedrock, which helps automate customer care and data analysis. The system ensures high availability and data security thanks to the VPC network separation design (Public/Private subnets).

The CI/CD process integrated with GitLab and ECR helps minimize downtime when updating new features, while Amazon CloudWatch provides comprehensive monitoring to detect incidents instantly. The cost model is optimized thanks to the use of Fargate (Serverless container) and Lambda (Pay-per-use), ensuring businesses only pay for the actual resources used. This investment not only resolves current operational challenges but also creates a solid technological foundation for long-term growth, with the expected Return on Investment (ROI) period significantly shortened.

### 3. Solution Architecture
The ReGenZ management platform utilizes a modern architecture deployed on AWS (Region **ap-southeast-2**), initiated by user access via **Amazon Route 53** at the Edge layer. The User Interface (Frontend) is hosted on **AWS Amplify Hosting**, which establishes a direct connection to **Amazon API Gateway** as the central entry point.

From the API Gateway, the data flow is strategically divided into three distinct paths:

* **AI Tasks:** Requests are routed to **AWS Lambda** to interact with **Amazon Bedrock** for generative AI capabilities.
* **Notification Tasks:** Asynchronous requests trigger **AWS Lambda** to handle email communications via **Amazon SES**.
* **Core Business Logic:** Traffic is directed through an **Application Load Balancer (ALB)** located in the Public Subnet, then forwarded to **Amazon ECS Fargate** instances secured within a Private Subnet.

**Data & Security:**

Relational data is persistently stored in **Amazon RDS** within the Private Subnet. To optimize security and performance, the architecture utilizes **VPC Endpoints** to keep traffic strictly within the AWS internal network:

* Static assets and images stored in **Amazon S3** are accessed securely via **S3 Endpoints**.
* Container images are pulled directly from **Amazon ECR** via **ECR Endpoints**.

By leveraging these endpoints, the system eliminates the need for a NAT Gateway, thereby reducing costs and minimizing public internet exposure.

**DevOps & Monitoring:**

* **GitLab** is used for source code management and CI/CD, automatically pushing deployments to Amplify (Frontend) and container images to ECR (Backend).

![APEX-EV Platform Architecture](/images/2-Proposal/ReGenZ.jpeg)

### AWS Services Used
- *Route 53*: DNS service, responsible for routing the domain (Edge layer) to the application. 
- *AWS Amplify Hosting*: Hosts the web interface (frontend) and can integrate with CDN/WAF. In the diagram, it receives traffic from Route 53.
- *Amazon API Gateway*: The main entry point (Gateway), receiving and routing all requests from the frontend/Amplify to processing services.  
- *AWS Lambda (Bedrock)*: Handles AI/Generative AI tasks (prediction/content generation) by communicating with Amazon Bedrock.
- *AWS Lambda (SES)*: Handles asynchronous tasks, such as processing notifications to send emails via AWS SES.
- *Amazon Bedrock*: General AI service (Gen AI), providing foundation models to execute intelligent business operations.
- *AWS SES*: Email sending service, performs the sending of notifications, quotes, or processing results from Lambda.
- *VPC*: Virtual network environment containing and protecting AWS resources (like ALB, ECS Fargate, RDS).
- *ALB (Application Load Balancer)*: Load balancer, distributing traffic from API Gateway to application containers running on ECS Fargate.
- *Amazon ECS Fargate*: Runs the backend application as containers without server management, handling core business logic.
- *Amazon RDS*: Provides a relational database, placed in a Private Subnet to store structured data.
- *Amazon S3*: Stores multimedia files like photos or other large data.
- *ECR*: Repository for application container images (Docker), used by ECS Fargate for deployment.
- *AWS CloudWatch*: Monitoring service, collecting logs and metrics from the entire system to track performance and detect issues.

### Component Design

* **Request Handling:**
    **Amazon Route 53** routes user domain requests to **AWS Amplify Hosting**, where the frontend interface is hosted. From there, API requests are forwarded to **Amazon API Gateway**, which acts as the central entry point to receive and route all incoming traffic.

* **Business Logic Processing:**
    * **Core Logic:** All primary business operations are handled by containerized applications running on **Amazon ECS Fargate**, deployed within a Private Subnet to ensure maximum security.
    * **AI & Asynchronous Tasks:** Generative AI tasks are processed by **AWS Lambda** interacting with **Amazon Bedrock**. Auxiliary tasks, such as email notifications, are handled by separate **AWS Lambda** functions triggering **Amazon SES**.

* **Network Infrastructure:**
    * **Public Subnet:** Hosts the **Application Load Balancer (ALB)** to receive and distribute external traffic.
    * **Private Subnet:** Dedicated to sensitive resources including ECS Fargate and Amazon RDS, ensuring they are isolated from direct public internet access.
    * **VPC Endpoints:** The system explicitly utilizes **S3 Endpoints** and **ECR Endpoints**. This design allows ECS Fargate to pull container images and access file storage securely within the AWS internal network, **without traversing the public internet**.

* **Data Storage:**
    * **Amazon RDS:** Stores sensitive, structured relational data.
    * **Amazon S3:** Stores multimedia files and large datasets.

* **Deployment and Monitoring:**
    The deployment pipeline is managed via **GitLab**, which triggers updates to AWS Amplify (Frontend) and pushes Docker images to **Amazon ECR** (Backend). **Amazon CloudWatch** provides comprehensive monitoring of performance logs and metrics across all services, from ECS and Lambda to RDS.

### 4. Technical Implementation

**Implementation Phases**

The development project for the ReGenZ Smart Electric Vehicle Maintenance Platform — including the integration of an AI virtual assistant and a service management system — is structured into 4 key phases:

1.  **Research and Architectural Design:** Research suitable technologies (React.js, Spring Boot, AWS Bedrock) and design a system architecture combining Containers (ECS) and Serverless (Lambda) on AWS (1 month prior to commencement).
2.  **Cost Estimation and Feasibility Check:** Use the AWS Pricing Calculator to estimate operating costs for core services such as ECS Fargate, RDS, and token costs for Amazon Bedrock, to propose the most financially feasible solution.
3.  **Architecture Adjustment for Optimization:** Refine the architecture, select appropriate configurations for ECS Fargate and RDS, and optimize Lambda runtime (timeouts) to balance AI processing performance and cost.
4.  **Development, Testing, and Deployment:** Develop the React.js application (Frontend) and Spring Boot (Backend), integrate the Bedrock Agent, deploy CI/CD pipelines via GitLab, package Docker images to ECR, and launch operations on ECS.

**Technical Requirements**

* **User Interface (Frontend):**
    Requires practical knowledge of **React.js** to build scheduling interfaces and chat features with the AI virtual assistant. Utilization of **AWS Amplify** is necessary to automate the deployment process (Hosting) and connect with **Amazon API Gateway** to send secure processing requests, ensuring a smooth user experience across all devices.

* **Core System (Backend & Infrastructure):**
    Requires in-depth knowledge of **Java/Spring Boot** to develop maintenance business logic. The application is packaged using **Docker**, with images stored on **Amazon ECR** and executed on **Amazon ECS Fargate**. The team must have a solid understanding of **Amazon RDS** for relational databases (storing vehicle profiles, maintenance history). Specifically, **AWS Lambda (Python)** programming skills are required to connect with **Amazon Bedrock** (for AI/Chatbot processing) and **AWS SES** (for sending asynchronous email notifications). Finally, system monitoring is implemented using **Amazon CloudWatch** integration.

### 5. Timeline & Milestones

**Project Timeline**

1.  **Phase 1 (Week 1-2): Design and Foundation:**
    * **Analyze & Design:** Define detailed AWS architecture (VPC, Subnets, Security Groups), design Database (RDS Schema), and define APIs (Swagger/OpenAPI).
    * **Infrastructure Setup:** Configure VPC (Public/Private Subnets) and IAM Roles.
    * **CI/CD Setup:** Configure Pipeline on GitLab to automatically build Docker Images, push to Amazon ECR, and deploy the Frontend to AWS Amplify.

2.  **Phase 2 (Week 3-4): Core Service Flow Development:**
    * **Customer Flow:** Develop Registration/Login, Vehicle Profile Management, and Appointment Scheduling (data stored in RDS).
    * **Service Advisor Flow:** Implement Vehicle Reception, Create Quotations, and Repair Orders.
    * **Technician Flow:** Develop features to View Task Lists, Update maintenance progress, and upload photos/videos to Amazon S3.

3.  **Phase 3 (Week 5-6): Administration & Advanced Feature Development:**
    * **Administration Module:** Build Report Dashboard, Spare Parts Management (Inventory), and Personnel Management.
    * **AI Integration:** Develop AWS Lambda functions to connect with **Amazon Bedrock Agent** (AI Chatbot) and expose endpoints via API Gateway.
    * **Notifications:** Develop AWS Lambda functions to trigger **AWS SES** for sending automatic emails/quotations to customers.
    * **Network Security:** Configure **VPC Endpoints (S3, ECR)** to ensure secure, private connectivity between Private Subnet resources and AWS services without using public internet.

4.  **Phase 4 (Week 7-8): Testing, Optimization, and Operation:**
    * **User Acceptance Testing (UAT):** Conduct internal testing to ensure the flow from Web -> API Gateway -> Lambda/ECS -> DB operates smoothly.
    * **Security Optimization:** Configure AWS WAF (to block SQL Injection, XSS) and review IAM access rights.
    * **Operational Monitoring:** Setup Dashboards on **Amazon CloudWatch** to track logs and metrics of ECS Fargate and Lambda.
    * **Official Deployment:** Launch the system for production use.
### 6. Budget Estimation
**Infrastructure Costs**

- Amazon ECS Fargate: ~11.00 USD/month.
- Application Load Balancer (ALB): ~16.43 USD/month.
- Amazon Bedrock (AI): ~5.00 USD/month (Calculated by Token count).
- AWS Lambda: 0.00 USD/month (Free Tier).
- Amazon RDS & ElastiCache: 0.00 USD/month (Free Tier).
- S3 Standard: ~0.15 USD/month.
- AWS Amplify & API Gateway: ~0.50 USD/month.
- Amazon CloudWatch: 0.00 USD/month (Free Tier).
- Amazon SES: 0.00 USD/month (Free Tier).

*Total:* ~32.63/month.

### 7. Risk Assessment

#### Risk Matrix
* **System Downtime:** High impact, Low probability.
* **Security Breach / Data Loss:** Very high impact, Low probability.
* **Operational Cost Overrun:** Medium impact, Medium probability.
* **Incorrect AI Response (Hallucination):** Medium impact, Medium probability.

#### Mitigation Strategies
* **System Stability:** Deploy infrastructure across **Multi-AZ** for RDS and ECS Fargate to ensure high availability. Use **Application Load Balancer (ALB)** for automatic traffic distribution and health checks.
* **Security:**
    * Backend resources (ECS, RDS) are placed in a **Private Subnet**, isolated from the direct internet access.
    * Access to Amazon S3 and ECR is secured via internal **VPC Endpoints**, ensuring data never leaves the AWS network.
    * Strict **IAM roles** are applied to Lambda functions and ECS tasks following the principle of least privilege.
* **Cost Management:** Use **AWS Budgets** to set alerts when costs exceed defined thresholds. Regularly monitor and optimize resources (right-sizing) to avoid waste.
* **AI Quality Control:** Limit **Amazon Bedrock Agent** response scope via strict Prompt Engineering (System Prompts) and only allow information retrieval from moderated Knowledge Bases.

#### Contingency Plans
* **Data Recovery:** Enable **Automated Backups** for Amazon RDS and utilize **Point-in-Time Recovery** to restore data to any specific moment in case of corruption.
* **Scalability:** Configure **Auto-scaling** for ECS Fargate to automatically expand the number of Tasks when traffic spikes, preventing overload.

### 8. Expected Outcomes
#### Technical Improvements: 
Technical Improvements: Successfully build a modern Hybrid Architecture combining Microservices (ECS Fargate) and Serverless (Lambda, Bedrock), ensuring flexible scalability without managing physical servers.

#### Long-term Value
Enhance customer experience: AI virtual assistant operating 24/7 helps reduce waiting time, increasing appointment conversion rates and car owner satisfaction.

Data assets: Maintenance history and interaction behavior data are centrally stored on RDS/S3, creating a premise for deploying AI Predictive Maintenance models for electric vehicle batteries and motors in the future.