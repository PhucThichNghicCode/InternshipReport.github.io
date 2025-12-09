---
title: "Event 1"
date: "2025-11-15"
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---



# Summary Report: “DevOps on AWS”

### Event Objectives

- Understand the DevOps mindset, culture, and key performance metrics (DORA).
- Master the AWS CI/CD toolchain for automated software delivery.
- Learn Infrastructure as Code (IaC) principles using CloudFormation and CDK.
- Explore containerization strategies and observability best practices on AWS.


### Key Highlights

#### DevOps Culture and Key Metrics

- **Mindset Shift**: Moving away from silos to shared responsibility between Development and Operations.
- **Key Metrics (DORA)**: Focusing on Deployment Frequency, Lead Time for Changes, Mean Time to Restore (MTTR), and Change Failure Rate to measure success.
- **Benefits**: Faster innovation, higher reliability, and improved collaboration.

#### AWS DevOps Services – CI/CD Pipeline 

We explored the full automation pipeline, moving from manual deployments to orchestration:

- **Source Control**: Utilizing **AWS CodeCommit** and implementing Git strategies like GitFlow and Trunk-based development.
- **Build & Test**: Using **AWS CodeBuild** for compiling source code, running tests, and producing software packages.
- **Deployment Strategies**: implementing **AWS CodeDeploy** for Blue/Green, Canary, and Rolling updates to minimize downtime.
- **Orchestration**: Tying it all together with **AWS CodePipeline**.

#### Infrastructure as Code (IaC)

Transitioning from manual console clicks to code-defined infrastructure:

- **AWS CloudFormation**: Using JSON/YAML templates to define resources, manage stacks, and detect infrastructure drift.
- **AWS CDK (Cloud Development Kit)**: Defining cloud resources using familiar programming languages (Python, TypeScript, Java) to create reusable constructs.
- **Tool Selection**: Discussing when to use declarative templates (CloudFormation) vs. imperative code (CDK).

#### Container Services on AWS 

- **Docker Fundamentals**: Packaging applications into lightweight, portable containers.
- **Registry**: Using **Amazon ECR** for secure image storage and vulnerability scanning.
- **Orchestration**: Comparing **Amazon ECS** (