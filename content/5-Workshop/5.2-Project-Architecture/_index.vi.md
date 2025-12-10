---
title : "Kiến trúc Dự án"
date :  "2025-11-05" 
weight : 2 
chapter : false
pre : " <b> 5.2 </b> "
---
 
#### Tổng quan Kiến trúc

Tài liệu này mô tả kiến trúc sản xuất đề xuất cho ReGenZet — hệ thống quản lý gara xe điện — và giải thích các lựa chọn công nghệ được sử dụng trong workshop.

- **Frontend:** Ứng dụng React SPA được triển khai và host trên **AWS Amplify** (CI/CD tự động, hosting tài nguyên và tích hợp CloudFront để cache toàn cầu và phân phối nhanh).
- **Backend:** Dịch vụ Spring Boot đóng gói dưới dạng Docker image và chạy trên **Amazon ECS (Fargate)**. Fargate loại bỏ gánh nặng quản lý host và cung cấp compute serverless có khả năng scale cho microservices.
- **Cơ sở dữ liệu:** **Amazon RDS (MySQL/Postgres)** đặt trong private subnets. RDS cung cấp backup tự động, snapshot, tùy chọn Multi-AZ và mã hóa khi lưu (KMS).
- **Quản lý API:** **Amazon API Gateway** làm điểm vào HTTPS duy nhất cho traffic client, xử lý routing, TLS termination và throttling.
- **Lưu trữ media:** **Amazon S3** lưu ảnh/ video kiểm tra xe và các media khác. Sử dụng presigned URLs để upload/download trực tiếp an toàn, giảm tải cho backend.
- **Thành phần bất đồng bộ / Serverless:**
  - Pipeline email: Backend (Spring) publish event đến **SNS**, kích hoạt **Lambda** gửi email qua **Amazon SES**.
  - Pipeline AI/Chat: Frontend → API Gateway → Lambda → **Amazon Bedrock** (hoặc LLM quản lý khác) cho inference và workflow hội thoại.
- **Mạng & Bảo mật:** Triển khai tài nguyên trong VPC với Public/Private subnets rõ ràng. Dùng **Security Groups** và **NACLs** để kiểm soát lưu lượng. Dùng **VPC Endpoints** (Gateway & Interface) cho S3 và truy cập dịch vụ riêng tư, giữ traffic bên trong mạng AWS.

#### Tại sao chọn kiến trúc này?

An ninh là ưu tiên
- Backend và RDS nằm trong private subnet và không mở port database ra Internet công cộng. API Gateway và frontend có load balancer terminate TLS ở edge, trong khi các dịch vụ nội bộ giao tiếp qua mạng riêng.

Tối ưu chi phí
- Fargate và Lambda cung cấp mô hình trả theo sử dụng. Khi phù hợp, cân nhắc Fargate Spot cho workloads không quan trọng và thiết lập autoscaling cùng lifecycle policies cho S3 để giảm chi phí.

Đơn giản vận hành & mô hình hiện đại
- Tách biệt rõ frontend/backend với API Gateway làm ingress duy nhất. Thành phần event-driven (SNS, Lambda) tách rời xử lý email/AI khỏi luồng request-response, cải thiện tính chịu lỗi và khả năng scale.

Nâng cao năng suất phát triển
- AWS Amplify đơn giản hóa CI/CD và hosting frontend. Quy trình container với Docker + ECR và ECS Fargate cho phép deploy lặp lại và dễ tái hiện cho backend.

Các điểm bảo mật & best-practice
- IAM theo nguyên tắc least-privilege cho các service và cross-account khi cần.
- Mã hóa do KMS quản lý cho RDS và đối tượng S3.
- Sử dụng WAF và rate-limiting trên API Gateway để giảm thiểu các cuộc tấn công ứng dụng.

Kiến trúc này cân bằng giữa bảo mật cấp doanh nghiệp và mô hình serverless tối ưu chi phí, đồng thời cung cấp lộ trình thực tế để áp dụng dần các tính năng nâng cao (observability, DR đa vùng, AI dựa trên Bedrock).

![overview](/images/aws_architecture-finish.drawio.png)