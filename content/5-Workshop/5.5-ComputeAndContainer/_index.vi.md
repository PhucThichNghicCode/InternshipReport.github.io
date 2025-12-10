---
title : "Compute và Container"
date : "2025-11-05"
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

#### IAM Role
- Tạo role `ecsTaskExecutionRole` cho AWS ECS (Elastic Container Service) để pull docker image và ghi log từ ECR repository
  - Mở trang [Amazon IAM](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/home)
  - Ở thanh điều hướng bên trái, chọn **Roles**, sau đó nhấp **Create role**
![overview](/images/ECS/ecs1.png)
  - Trong màn hình tạo, chọn **AWS service** và chọn Use case là **EC2** rồi nhấp **Next**
![overview](/images/ECS/ecs2.png)
  - Ở bước Add permissions, tìm `AmazonECSTaskExecutionRolePolicy` và chọn policy này, rồi nhấp **Next**
![overview](/images/ECS/ecs3.png)
  - Ở phần Name, review, and create, đặt tên Role là `ecsTaskExecutionRole`, sau đó nhấp **Create role**
![overview](/images/ECS/ecs4.png)
- Tạo role `FargateTaskRole` để cho phép Fargate truy cập tài nguyên
  - Ở thanh điều hướng bên trái, chọn **Roles**, sau đó nhấp **Create role**
![overview](/images/ECS/ecs1.png)
  - Trong màn hình tạo, chọn **AWS service** và Use case là **EC2** rồi nhấp **Next**
![overview](/images/ECS/ecs2.png)
  - Ở bước Add permissions, tìm và thêm `AmazonS3FullAccess`, `AmazonSESFullAccess` và `AmazonSNSFullAccess`, sau đó nhấp **Next**
  - Review và đặt tên Role là `FargateTaskRole`, sau đó nhấp **Create role**

#### ECS Cluster
- Tạo ECS Cluster để chạy Fargate
  - Mở trang [Amazon ECS](https://ap-southeast-1.console.aws.amazon.com/ecs/v2/getStarted?region=ap-southeast-1)
  - Ở thanh điều hướng bên trái, chọn **Clusters**, sau đó nhấp **Create Cluster**
![overview](/images/ECS/ecs5.png)
  - Trong màn hình tạo, điền tên Cluster
  - Ở phần Infrastructure, chọn **Fargate only**
  - Sau đó nhấp **Create**
![overview](/images/ECS/ecs6.png)

#### Task definitions
- Tạo Task definition — định nghĩa container
  - Ở thanh điều hướng của ECS console, chọn **Task definitions**, sau đó nhấp **Create new task definitions**
![overview](/images/ECS/ecs7.png)
  - Trong màn hình tạo, nhập `Task definition family`
  - Chọn **AWS Fargate** trong phần Launch type của Infrastructure requirements
  - Chọn Task role là `FargateTaskRole`
  - Chọn Task execution role là `ecsTaskExecutionRole`
![overview](/images/ECS/ecs8.png)
  - Ở phần Container, nhập tên và URL của ECR, sau đó thêm Environment variable
![overview](/images/ECS/ecs9.png)
  - Kéo xuống cuối trang và nhấp **Create**

#### ECS Service
- Tạo ECS Service để chạy Task definitions
  - Ở thanh điều hướng bên trái, chọn **Clusters**, sau đó mở Cluster đã tạo
  - Trong tab **Services**, nhấp **Create**
![overview](/images/ECS/ecs10.png)
  - Trong màn hình tạo, chọn `Task definition family`
  - Chọn `Task definition revision`
  - Điền tên Service
  - Ở Compute options, chọn **Capacity provider strategy**
  - Ở Capacity provider, chọn **FARGATE**
  - Ở Platform version, chọn **LATEST**
![overview](/images/ECS/ecs11.png)
  - Ở Deployment configuration, chọn Scheduling strategy là **Replica** và Desired tasks = 1
![overview](/images/ECS/ecs12.png)
  - Sau đó nhấp **Create**
![overview](/images/ECS/ecs13.png)