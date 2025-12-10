---
title : "Giới thiệu"
date :  "2025-11-05"
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

#### ApexEV — Giới thiệu Workshop (Tóm tắt)

ApexEV là nền tảng quản lý gara xe điện cấp doanh nghiệp, số hóa hoạt động xưởng, cải thiện trải nghiệm khách hàng và giúp kỹ thuật viên làm việc nhanh hơn, an toàn hơn.

Tại sao làm workshop này:
- Giải quyết các vấn đề thường gặp tại gara: quy trình thủ công, thiếu minh bạch, chăm sóc khách hàng kém và rủi ro dữ liệu.
- Xây dựng backend trên cloud bảo mật, có khả năng mở rộng và tối ưu chi phí ngay từ đầu theo best practices của AWS.

Tóm tắt kiến trúc chính:
- **Frontend:** Ứng dụng React host trên **AWS Amplify** (CI/CD + CloudFront).
- **Backend:** Các service Spring Boot chạy trên **ECS (Fargate)** — container không cần quản lý server.
- **Cơ sở dữ liệu:** **Amazon RDS** (private subnets, backup tự động, mã hóa KMS).
- **Lưu trữ:** **Amazon S3** cho media (dùng presigned URLs để upload trực tiếp).
- **API & Bất đồng bộ:** **API Gateway** làm ingress HTTPS; **SNS → Lambda → SES** cho email; **Lambda → Bedrock** cho AI/chat.
- **Mạng & Bảo mật:** VPC (public/private), Security Groups, VPC Endpoints, WAF và IAM theo nguyên tắc least-privilege.

Lợi ích chính:
- Ưu tiên bảo mật: backend và DB ở private; edge service terminate TLS và áp dụng WAF/rate limits.
- Tối ưu chi phí: Fargate + Lambda tính phí theo sử dụng; lifecycle rules và autoscaling giúp tiết giảm chi phí.
- Hiện đại & mô-đun: tách biệt frontend/backend, luồng bất đồng bộ giúp tăng chịu lỗi và khả năng mở rộng.

Mục tiêu workshop:
- Cấu hình mạng và dịch vụ an toàn, triển khai frontend + backend, kết nối RDS và S3, tích hợp pipeline email và AI. Mỗi module có các bước hướng dẫn, cấu hình khuyến nghị và hướng dẫn dọn dẹp.