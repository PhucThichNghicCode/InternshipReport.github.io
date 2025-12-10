---
title : "Tạo AWS Load Balancers"
date : "2025-11-05"
weight : 6
chapter : false
pre : " <b> 5.6 </b> "
---

1. Mở trang [Amazon EC2](https://ap-southeast-1.console.aws.amazon.com/ec2/home?region=ap-southeast-1#Overview:)
2. Ở thanh điều hướng bên trái, chọn **Load Balancers**, sau đó nhấp **Create load balancer**
![overview](/images/ALB/a1.png)
3. Chọn **Application Load Balancer**
![overview](/images/ALB/a2.png)
4. Trong màn hình tạo, điền tên Load balancer
5. Chọn **Scheme** là Internet-facing, sau đó chọn VPC
![overview](/images/ALB/a3.png)
6. Ở phần Availability Zones and subnets, chọn hai AZ và chọn hai subnet trong public
7. Ở phần Security groups, chọn `alb-sg`
![overview](/images/ALB/a4.png)
8. Kéo xuống cuối trang và nhấp **Create load balancer**
9. ![overview](/images/ALB/a5.png)
