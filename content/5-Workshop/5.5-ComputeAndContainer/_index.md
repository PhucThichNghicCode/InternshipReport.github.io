---
title : "Compute And Container"
date : "2025-11-05"
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

#### IAM Role
+ Create ecsTaskExecutionRole role to AWS ECS (Elastic Container Service) pull docker image and write logs from ECR repository
   - Open the [Amazon IAM](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/home)
   - In left navbar, choose Roles, then click **Create role**
![overview](/images/ECS/ecs1.png)
   - In create console, choose AWS service and select Use case is EC2 then click **Next**
![overview](/images/ECS/ecs2.png)
   - In Add permissions step, search AmazonECSTaskExecutionRolePolicy and select this Policy, then click **Next**
![overview](/images/ECS/ecs3.png)
   - In Name, review, and create, fill in Role name is ecsTaskExecutionRole , then click **Create role**
![overview](/images/ECS/ecs4.png)
+ Create FargateTaskRole role to allow Fargate access resource
  - In left navbar, choose Roles, then click **Create role**
![overview](/images/ECS/ecs1.png)
  - In create console, choose AWS service and select Use case is EC2 then click **Next**
![overview](/images/ECS/ecs2.png)
  - In Add permissions step, search AmazonS3FullAccess, AmazonSESFullAccess and AmazonSNSFullAccess, then click **Next**
  - Review and fill in Role name is FargateTaskRole, then click **Create role**

#### ECS Cluster
+ Create ECS Cluster is place to Fargate running
  - Open the [Amazon ECS](https://ap-southeast-1.console.aws.amazon.com/ecs/v2/getStarted?region=ap-southeast-1)
  - In left navbar, choose Clusters, then click **Create Cluster**
![overview](/images/ECS/ecs5.png)
  - In create console, fill in Cluster name
  - In Infrastructure, select Fargate only
  - Then click **Create**
![overview](/images/ECS/ecs6.png)

#### Task definitions
+ Create Task definitions is a container design
  - In left navbar of ECS console, choose Task definitions, then click **Create new task definitions
![overview](/images/ECS/ecs7.png)
  - In create console, fill in Task definition family
  - Select AWS Fargate in Launch type of Infrastructure requirements
  - Choose Task role is FargateTaskRole
  - Choose Task execution role is ecsTaskExecutionRole
![overview](/images/ECS/ecs8.png)
  - In Container, fill in name and URL of ECR, then Add Environment variable
![overview](/images/ECS/ecs9.png)
  - Scroll down to the bottom and click **Create**

#### ECS Service
+ Create ECS Service to run Task definitions
  - In left navbar, choose Clusters, then click Cluster created
  - In tab Sevices, click **Create**
![overview](/images/ECS/ecs10.png)
  - In create console, choose Task definition family
  - Choose Task definition revision
  - Fill in Service name
  - In Compute options choose Capacity provider strategy
  - In Capacity provider, select FARGATE
  - In Platform version, select LATEST
![overview](/images/ECS/ecs11.png)
  - In Deployment configuration, Scheduling strategy is Replica and Desired tasks is 1
![overview](/images/ECS/ecs12.png)
  - Then click **Create**
![overview](/images/ECS/ecs13.png)

