---
title : "Create RDS"
date : "2025-11-05"
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

1. Open the [Amazon Aurora and RDS](https://ap-southeast-1.console.aws.amazon.com/rds/home?region=ap-southeast-1#)
2. In left navbar, choose Databases, then click **Create database**
![overview](/images/RDS/r1.png)
3. In create console, choose **Full configuration**
4. Then choose database type is MySQL
5. Choose Templates is Production
![overview](/images/RDS/r2.png)

6. Select Availability and durability is Multi-AZ DB cluster deployment (3 instances)
7. Fill DB instance identifier
8. Choose Self managed
9. Spacific Master password
![overview](/images/RDS/r3.png)
10. In Instance configuration, choose db.m5d.large
11. In Storage, Allocated storage fill in 100 and Provisioned IOPS is 1000
12. In Connectivity, choose Don't connect to an EC2, then select VPC
![overview](/images/RDS/r4.png)
13. Choose DB subnet group and then in VPC security group (firewall) select **Choose existing**
14. Select rds-sg
15. Then click **Create database**
![overview](/images/RDS/r5.png)