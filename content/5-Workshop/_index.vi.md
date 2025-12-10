---
title: "Workshop"
date: "2025-11-05"
weight: 5
chapter: false
pre: " <b> 5 </b> "
---

# Triển khai hệ thống ReGenZet lên AWS

#### Tổng quan

ReGenZet là nền tảng quản lý gara xe điện cấp doanh nghiệp. Mục tiêu của workshop này là thiết kế và triển khai một hạ tầng đám mây trên AWS đảm bảo an toàn, tối ưu chi phí và tự động hóa cao để lưu trữ frontend, backend, lưu trữ media và các chức năng serverless AI/ML của ApexEV.

Nguyên tắc kiến trúc chính:
- **An ninh là ưu tiên:** IAM theo nguyên tắc ít quyền nhất, mã hóa dữ liệu khi lưu và truyền, cô lập mạng và kiểm soát điểm truy cập dịch vụ.
- **Tối ưu chi phí:** sử dụng dịch vụ quản lý theo mô hình trả theo mức sử dụng, right-sizing và chính sách lifecycle tự động cho lưu trữ và compute.
- **Tự động hóa & Quan sát:** Infrastructure-as-Code, pipeline CI/CD, logging tập trung và cảnh báo tự động.

Dịch vụ lõi sử dụng trong workshop:
- **AWS ECS (Fargate)** — chạy microservices backend mà không cần quản lý máy chủ.
- **AWS Amplify** — host frontend, cung cấp CI/CD cho client web và quản lý hosting.
- **Amazon RDS** — cơ sở dữ liệu quan hệ được quản lý cho dữ liệu giao dịch.
- **Amazon S3** — lưu trữ đối tượng cho media, sao lưu và static assets.
- **AWS Lambda** — hàm serverless cho pipeline AI/ML, thông báo và tác vụ nền.

Workshop này bao gồm các module thực hành bao quát toàn bộ stack và các best-practice cho từng lớp.

#### Nội dung

1. [Tổng quan Workshop](5.1-Workshop-overview)
2. [Kiến trúc Dự án](5.2-Project-Architecture/)
3. [VPC của Dự án](5.3-VPC/)
4. [Cơ sở dữ liệu và Lưu trữ](5.4-DatabaseAndStorage/)
5. [Compute và Container](5.5-ComputeAndContainer/)
6. [Tạo Load Balancer](5.6-CreateALB/)
7. [Tạo Amplify và API Gateway](5.7-AmplifyAndAPIGateway/)
8. [Hướng dẫn triển khai Frontend và Backend](5.8-Instruct-deploy-be-fe/)
