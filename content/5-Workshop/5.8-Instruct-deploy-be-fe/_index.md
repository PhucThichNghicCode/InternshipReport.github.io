---
title : "Instruct Deploy BackEnd And Frontend"
date : "2025-11-05"
weight : 6
chapter : false
pre : " <b> 5.8 </b> "
---
#### Deploy Frontend Instructure
+ Deploy Frontend to Amplify
1. Open terminal in your code from your compute
2. Commit and Push to branch of Gitlab which define in Amplify
3. Amplify auto CICD and deploy your Frontend
4. Wait 3-5 minutes
#### Deploy Backend Instructure
+ Deploy Backend to Fargate  
1. Open the [Amazon ECR](https://ap-southeast-1.console.aws.amazon.com/ecr/get-started?region=ap-southeast-1)
2. In left navbar, choose Repository and select ECR repository created 
![overview](/images/deploy/d1.png)
3. Open terminal in your code from your compute
4. Write syntax "AWS configure" to setup access key and secret key of your AWS account
![overview](/images/deploy/d3.png)
5. After setup AWS account for CLI, comeback console of ECR Repository
6. Click View push command, you will see syntax to push docker image to ECR repository
7. Then copy and paste it to terminal of your Backend project
![overview](/images/deploy/d2.png)
- Fisnish this step, AWS ECS Fargate will auto pull and run image have tag latest

+ Finally,Congratulations for finish this workshop with deploy success project to AWS. 
