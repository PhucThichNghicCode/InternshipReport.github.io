---
title : "Tạo RDS"
date : "2025-11-05"
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

1. Mở trang [Amazon Aurora and RDS](https://ap-southeast-1.console.aws.amazon.com/rds/home?region=ap-southeast-1#)
2. Ở thanh điều hướng bên trái, chọn **Databases**, sau đó nhấp **Create database**
![overview](/images/RDS/r1.png)
3. Trong màn hình tạo, chọn **Full configuration**
4. Chọn loại cơ sở dữ liệu là **MySQL**
5. Chọn Templates là **Production**
![overview](/images/RDS/r2.png)

6. Ở phần Availability and durability, chọn **Multi-AZ DB cluster deployment (3 instances)**
7. Điền `DB instance identifier`
8. Chọn chế độ **Self managed**
9. Nhập `Master password`
![overview](/images/RDS/r3.png)
10. Trong Instance configuration, chọn `db.m5d.large`
11. Trong Storage, đặt **Allocated storage** là `100` và **Provisioned IOPS** là `1000`
12. Trong Connectivity, chọn **Don't connect to an EC2**, sau đó chọn VPC
![overview](/images/RDS/r4.png)
13. Chọn DB subnet group và ở phần VPC security group (firewall) chọn **Choose existing**
14. Chọn `rds-sg`
15. Sau đó nhấp **Create database**
![overview](/images/RDS/r5.png)
