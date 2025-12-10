---
title : "Create Security Groups"
date :  "2025-11-05" 
weight : 2
chapter : false
pre : " <b> 5.3.3 </b> "
---
1. Open the [Amazon VPC console](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#Home:)
2. Choose **Security Groups**  -> click **Create security group**
![endpoint](/images/Sg/sg1.png)
3. In Create Security group
   - Spacific name of Security group
   - Choose VPC created
   - Add rule Inbound and Outbound for Security Group
![endpoint](/images/Sg/sg3.png)
#### In ReGenZet project we have 4 Security groups, which are fargate-sg, rds-sg, alb-sg and endpoint-sg.
1. fargate-sg is security group for AWS ECS Fargate
   - Do again step 1 -> 3
   - Choose fargate-sg created
![endpoint](/images/Sg/sg5.png)
   - Inbount Add rule security group of Application Load Balancer (ALB) is alb-sg
   - Outbound Add rule security group of MySQl (rds-sg) and HTTPS
   - Click **Create security group**
![endpoint](/images/Sg/sg2.png)
2. rds-sg is security group for AWS RDS
   - Do again step 1 -> 3
   - Choose rds-sg created
![endpoint](/images/Sg/sg4.png)
   - Inbound Add rule security group of MySQL like this instruct
   - Outbound is not Add rule
   - Click **Create security group**
![endpoint](/images/Sg/sg6.png)
3. alb-sg is security group for AWS Application Load Balancer (ALB)
   - Do again step 1 -> 3
   - Inbound Add rule HTTPS and HTTP type like this instruct
![endpoint](/images/Sg/sg7.png)
   - Outbound Add rule security group of AWS ECS Fargate (fargate-sg)
![endpoint](/images/Sg/sg8.png)
   - Click **Create security group**
4. endpoint-sg is security group for VPC Endpoints
   - Do again step 1 -> 3
   - Inbound Add rule security group of AWS ECS Fargate (fargate-sg)
![endpoint](/images/Sg/sg9.png)
   - Outbound is not Add rule
 