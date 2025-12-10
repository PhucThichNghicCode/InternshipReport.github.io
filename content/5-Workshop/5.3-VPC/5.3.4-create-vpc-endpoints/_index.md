---
title : "Create VPC Endpoints"
date :  "2025-11-05" 
weight : 2
chapter : false
pre : " <b> 5.3.4 </b> "
---
1. Open the [Amazon VPC console](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#Home:)
2. Choose **Endpoints**  -> click **Create endpoints**
![endpoint](/images/Endpoints/e1.png)
3. In Create console:
   - Fill name of VPC endpoint
   - Type is AWS Services
   - Search
![endpoint](/images/Endpoints/e2.png)
4. In this project we have 5 VPC Endpoints
   1. VPC Enpoint S3 Gateway (apexev-s3-gateway)
      - In search box -> com.amazonaws.ap-southeast-1.s3
      - Choose type Gateway
      - Choose VPC created
      - Choose Route table private
      - Policy is Full access
      - Click **Create endpoint**
![endpoint](/images/Endpoints/e3.png)
   2. VPC Enpoint ECR API & DKR Interface
      - In search box -> ecr
      - Will see ecr.api and ecr.dkr interface
      - Do this twice and choose a different one each time
      - Choose VPC created
      - Tick two subnet private in two difference AZ
      - Choose endpoint-sg
      - Click **Create endpoint**
![endpoint](/images/Endpoints/e4.png)
   3. VPC Enpoint Logs
      - In search box -> com.amazonaws.ap-southeast-1.logs
      - Choose com.amazonaws.ap-southeast-1.logs
      - Choose VPC created
      - Tick two subnet private in two difference AZ
      - Choose endpoint-sg
      - Click **Create endpoint**
![endpoint](/images/Endpoints/e5.png)
   4. VPC Enpoint AWS SNS
      - In search box -> com.amazonaws.ap-southeast-1.sns
      - Choose com.amazonaws.ap-southeast-1.sns
      - Choose VPC created
      - Tick two subnet private in two difference AZ
      - Choose endpoint-sg
      - Click **Create endpoint**
![endpoint](/images/Endpoints/e6.png)