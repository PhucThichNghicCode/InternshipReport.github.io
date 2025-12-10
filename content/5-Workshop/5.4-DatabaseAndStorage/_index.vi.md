---
title : "Cơ sở dữ liệu và lưu trữ"
date : "2025-11-05"
weight : 4
chapter : false
pre : " <b> 5.4 </b> "
---

#### Tổng quan
- Dự án này sử dụng ba dịch vụ lưu trữ dữ liệu chính:
  + **AWS RDS** (Relational Database Service): dịch vụ quản lý cơ sở dữ liệu, được đặt trong Private Subnet.
  + **AWS S3** (Simple Storage Service): lưu trữ hình ảnh và video.
  + **AWS ECR** (Elastic Container Registry): lưu trữ Docker images.

#### Nội dung
- [Tạo RDS](5.4.1-create-rds/)
- [Tạo S3](5.4.2-create-S3/)
- [Tạo ECR](5.4.3-create-ecr/)
