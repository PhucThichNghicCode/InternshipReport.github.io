---
title : "Hướng dẫn Deploy BackEnd And Frontend"
date : "2025-11-05"
weight : 6
chapter : false
pre : " <b> 5.8 </b> "
---

#### Triển khai Frontend
- Triển khai Frontend lên Amplify
1. Mở terminal trong thư mục mã nguồn trên máy của bạn
2. Commit và push lên branch đã được cấu hình trong Amplify
3. Amplify sẽ tự động chạy CI/CD và triển khai Frontend
4. Chờ khoảng 3–5 phút để quá trình hoàn tất

#### Triển khai Backend
- Triển khai Backend lên Fargate
1. Mở trang [Amazon ECR](https://ap-southeast-1.console.aws.amazon.com/ecr/get-started?region=ap-southeast-1)
2. Ở thanh điều hướng bên trái, chọn **Repository** và mở ECR repository đã tạo
![overview](/images/deploy/d1.png)
3. Mở terminal trong thư mục mã nguồn Backend trên máy của bạn
4. Chạy `aws configure` để cấu hình Access Key và Secret Key cho tài khoản AWS
![overview](/images/deploy/d3.png)
5. Sau khi cấu hình CLI, quay lại trang console của ECR Repository
6. Nhấp **View push commands**, bạn sẽ thấy các lệnh để push Docker image lên ECR
7. Sao chép và dán các lệnh đó vào terminal của dự án Backend
![overview](/images/deploy/d2.png)

- Sau khi hoàn tất các bước này, AWS ECS Fargate sẽ tự động pull và chạy image có tag `latest`.

Chúc mừng — bạn đã hoàn thành workshop và triển khai thành công dự án lên AWS.
