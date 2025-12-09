---
title: "Event 2"
date: "2025-11-29"
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---

# Summary Report: “AWS Well-Architected Security Pillar”

### Event Objectives

- Deep dive into the 5 core pillars of AWS Security: IAM, Detection, Infrastructure Protection, Data Protection, and Incident Response.
- Understand modern security principles: Zero Trust, Least Privilege, and Defense in Depth.
- Learn to automate security checks and incident response using AWS native tools.
- Identify common cloud threats in the Vietnam market and how to mitigate them.


### Key Highlights

#### Opening & Security Foundation 

- **Core Principles**: Moving beyond perimeter security to **Defense in Depth**, **Zero Trust** architecture, and strictly enforcing **Least Privilege**.
- **Shared Responsibility Model**: Clarifying what AWS secures (of the cloud) vs. what the customer secures (in the cloud).
- **Local Context**: Discussing top security threats specifically targeting cloud environments in Vietnam.

#### Pillar 1: Identity & Access Management (IAM) 

- **Modern IAM Architecture**: Moving away from long-term credentials (IAM Users) to temporary credentials (IAM Roles).
- **Governance**: Using **IAM Identity Center** for SSO and centralized management.
- **Control**: Implementing **SCPs (Service Control Policies)** and permission boundaries for multi-account environments.
- **Best Practices**: Enforcing MFA, regular credential rotation, and using **Access Analyzer** to validate policies.

#### Pillar 2: Detection & Continuous Monitoring

- **Logging Strategy**: "Log everything" approach using **CloudTrail** (org-level), **VPC Flow Logs**, and ALB/S3 logs.
- **Threat Detection**: Utilizing **Amazon GuardDuty** for intelligent threat detection.
- **Centralization**: Aggregating findings in **AWS Security Hub**.
- **Detection-as-Code**: Automating alerts using **Amazon EventBridge** to trigger immediate notifications.

#### Pillar 3: Infrastructure Protection 

- **Network Security**: Implementing rigorous VPC segmentation and distinguishing strictly between private and public subnets.
- **Firewalls**: Understanding the layered defense using **WAF** (Web Application Firewall), **AWS Shield** (DDoS), and **Network Firewall**.
- **Access Control**: Differentiating between Stateful (Security Groups) and Stateless (NACLs) firewalls.

#### Pillar 4: Data Protection

- **Encryption Strategy**:
    - **At-rest**: Encrypting S3 buckets, EBS volumes, RDS, and DynamoDB.
    - **In-transit**: TLS/SSL enforcement.
- **Key Management**: Managing keys via **AWS KMS** (Key Management Service), focusing on grants and rotation policies.
- **Secrets Management**: Removing hardcoded credentials from code by using **Secrets Manager** and **Systems Manager Parameter Store**.

#### Pillar 5: Incident Response (IR) 

- **IR Lifecycle**: Preparation -> Detection & Analysis -> Containment, Eradication & Recovery -> Post-Incident Activity.
- **Automation**: Using **AWS Lambda** and **Step Functions** to auto-remediate issues (e.g., isolating a compromised EC2 instance).
- **Playbooks**: Walkthrough of standard responses for scenarios like "Compromised IAM Key," "S3 Public Exposure," and "Malware Detection."

### Key Takeaways

#### Identity is the New Perimeter

- Identity management is the most critical line of defense. Long-term access keys are a major risk; utilizing IAM Roles and SSO is mandatory for a modern architecture.

#### Visibility is Paramount

- You cannot protect what you cannot see. Enabling centralized logging (CloudTrail, Config) and threat detection (GuardDuty) is the first step in any security strategy.

#### Automate Security

- Humans are slow; attacks are fast. Security responses (locking down a user, blocking an IP) should be automated via code (Lambda/EventBridge) wherever possible.

### Applying to Work

- **Audit IAM Policies**: Review current permissions to ensure "Least Privilege" and remove unused IAM Users.
- **Enable GuardDuty**: Activate GuardDuty in the main region to detect anomalies immediately.
- **Encrypt Data**: Ensure all new S3 buckets and EBS volumes have default encryption enabled via KMS.
- **Develop IR Playbooks**: Draft a basic Incident Response plan for "S3 Public Leak" and "Compromised Credentials" scenarios.

### Event Experience

The **"AWS Well-Architected Security Pillar"** workshop was an intensive and highly focused morning session. It provided a structured approach to security that is often overlooked in rushed development cycles.

#### Deep Dive into "Defense in Depth"
- The session on **Infrastructure Protection** clarified how to layer security controls (WAF -> NACL -> SG) so that if one fails, others are still in place.

#### Practical Focus on Automation
- Seeing the **"Mini Demo"** on validating IAM policies and simulating access was very helpful. It showed that security can be tested just like software code.
- The **Incident Response** segment changed my perspective: instead of waking up at 3 AM to fix a hack manually, we can write Lambda functions to contain threats automatically.

#### Local Context
- Hearing about **common pitfalls in Vietnamese enterprises** helped me relate the theoretical concepts to the actual risks our business faces daily.
