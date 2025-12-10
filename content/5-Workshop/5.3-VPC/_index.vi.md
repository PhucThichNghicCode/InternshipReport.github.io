---
title : "VPC"
date : "2025-11-05"
weight : 3
chapter : false
pre : " <b> 5.3 </b> "
---

#### Giới thiệu
**VPC (Virtual Private Cloud)** là một không gian mạng ảo cô lập về mặt logic trong AWS Cloud. Nó hoạt động giống như một trung tâm dữ liệu cá nhân trên đám mây, cho phép bạn kiểm soát hoàn toàn môi trường mạng.
- Thành phần lõi:
  - Route Table
  - Subnets
  - Internet Gateway
  - Security Groups
#### Tạo VPC
1. Mở [Amazon VPC console](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#vpcs:)
2. Chọn **Your VPCs**, sau đó nhấp **Create VPC**
![overview](/images/VPC/VPC1.png)
3. Trong màn hình tạo VPC:
   - Đặt tên trong Name tag: `my-vpc-01`
   - IPv4 CIDR: `10.0.0.0/16`
   - Sau đó nhấp **Create VPC**
![overview](/images/VPC/VPC2.png)
#### Nội dung
- [Create Route Table & Internet Gateway](3.1-create-route-table/)
- [Create Subnets](3.2-create-subnets/)
- [Create Security Groups](3.3-create-security-groups/)
- [Create VPC Endpoints](3.3-create-vpc-endpoints/)