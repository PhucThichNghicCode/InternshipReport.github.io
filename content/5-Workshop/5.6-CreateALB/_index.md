---
title : "Create AWS Load Balancers"
date : "2025-11-05"
weight : 6
chapter : false
pre : " <b> 5.6 </b> "
---

1. Open the [Amazon EC2](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Overview:)
2. In left navbar, choose Load Balancers, then click **Create load balancer**
![overview](/images/ALB/a1.png)
3. Choose Application Load Balancer
![overview](/images/ALB/a2.png)
4. In create console, fill in Load balancer name
5. Scheme is Internet-facing, then select VPC
![overview](/images/ALB/a3.png)
6. In Availability Zones and subnets choose two AZ and select two subnet in public\
7. Security groups select alb-sg
![overview](/images/ALB/a4.png)
8. Then scroll down to the bottom and click **Create load balancer**
9. ![overview](/images/ALB/a5.png)