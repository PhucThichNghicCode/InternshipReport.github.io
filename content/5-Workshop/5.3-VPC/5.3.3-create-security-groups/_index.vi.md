---
title : "Tạo Security Groups"
date :  "2025-11-05"
weight : 2
chapter : false
pre : " <b> 5.3.3 </b> "
---

1. Mở [Amazon VPC console](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#Home:)
2. Chọn **Security Groups** -> click **Create security group**
![endpoint](/images/Sg/sg1.png)
3. Trong Create Security group:
- Đặt tên cho Security group
- Chọn VPC đã tạo
- Thêm rule Inbound và Outbound cho Security Group
![endpoint](/images/Sg/sg3.png)

#### Trong dự án ReGenZet chúng ta có 4 Security Groups: `fargate-sg`, `rds-sg`, `alb-sg` và `endpoint-sg`.

1. `fargate-sg` — security group cho AWS ECS Fargate
- Lặp lại các bước 1 -> 3
- Chọn `fargate-sg` đã tạo
![endpoint](/images/Sg/sg5.png)
- Inbound: thêm rule cho security group của Application Load Balancer (`alb-sg`)
- Outbound: thêm rule tới MySQL (`rds-sg`) và HTTPS
- Click **Create security group**
![endpoint](/images/Sg/sg2.png)

2. `rds-sg` — security group cho AWS RDS
- Lặp lại các bước 1 -> 3
- Chọn `rds-sg` đã tạo
![endpoint](/images/Sg/sg4.png)
- Inbound: thêm rule cho MySQL theo hướng dẫn
- Outbound: không cần thêm rule
- Click **Create security group**
![endpoint](/images/Sg/sg6.png)

3. `alb-sg` — security group cho Application Load Balancer (ALB)
- Lặp lại các bước 1 -> 3
- Inbound: thêm rule HTTPS và HTTP theo hướng dẫn
![endpoint](/images/Sg/sg7.png)
- Outbound: thêm rule tới security group của ECS Fargate (`fargate-sg`)
![endpoint](/images/Sg/sg8.png)
- Click **Create security group**

4. `endpoint-sg` — security group cho VPC Endpoints
- Lặp lại các bước 1 -> 3
- Inbound: thêm rule cho security group của ECS Fargate (`fargate-sg`)
![endpoint](/images/Sg/sg9.png)
- Outbound: không cần thêm rule
