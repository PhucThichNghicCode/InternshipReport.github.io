---
title : "Create Amplify And API Gateway"
date : "2025-11-05"
weight : 6
chapter : false
pre : " <b> 5.7 </b> "
---
#### AWS Amplify
+ Create Amplify to deploy Frontend
1. Open the [Amazon Amplify](https://ap-southeast-1.console.aws.amazon.com/amplify/home?region=ap-southeast-1#)
2. Click **Create new app**
![overview](/images/Amplify/am1.png)
3. In create console, choose Gitlab, then click **Next**
![overview](/images/Amplify/am2.png)
4. In step Add repository and branch, login with Gitlab, then choose Git Repository and branch need to deploy
5. Then click **Next**
![overview](/images/Amplify/am3.png)
![overview](/images/Amplify/am4.png)
6. Setting format with your type code in your Frontend, then click **Next**
![overview](/images/Amplify/am5.png)
7. Review and click **Save and deploy**
![overview](/images/Amplify/am6.png)
- After this step wait about 3-5 minutes to deploy and you can access your app from internet

#### API Gateway
+ API Gateway is service to transfer HTTP and HTTPS between Amplify (Frontend) and Fargate (Backend)
1. Open the [Amazon API Gateway](https://ap-southeast-1.console.aws.amazon.com/apigateway/main/welcome?region=ap-southeast-1)
2. Click **Create API**
![overview](/images/Amplify/api1.png)
3. Choose REST API and click **Build**
![overview](/images/Amplify/api2.png)
4. In create console, choose API details is New API
5. Fill in API name
6. API endpoint type is Regional
7. Security policy is SecurityPolicy_TLS13_1_2_2021_06
8. Then click **Create API**
![overview](/images/Amplify/api3.png)
9.  In left navbar, choose APIs and select API Gateway created
![overview](/images/Amplify/api8.png)
10.  In console API Gateway click Create Resource then fill in Resource name and click **Create resource**
![overview](/images/Amplify/api6.png)
![overview](/images/Amplify/api7.png)
11.  In console resource API Gateway, click **Create method**
![overview](/images/Amplify/api9.png)
12. In Method details choose ANY, and Integration type is HTTP Proxy
13. HTTP method select ANY, and Endpoint URL is Endpoints of ALB
14. Then scroll down to the bottom and click **Create method**
![overview](/images/Amplify/api10.png)
15. In resource proxy, create method
![overview](/images/Amplify/api12.png)
16. In create console, choose Integration type is Mock and Method type is OPTIONS
17. Then click **Create method**
+ Now we finish setup API Gateway for project