---
title : "Tạo Subnet"
date :  "2025-11-05"
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

#### Tạo Public Subnet

1. Mở [Amazon VPC console](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#Home:)
2. Chọn **Subnets** -> click **Create subnet**
![endpoint](/images/Subnets/s1.png)
3. Trong form Create subnet:
- Chọn VPC đã tạo
- Điền tên subnet
- Chọn Availability Zone
- Nhập CIDR IPv4 cho subnet
![endpoint](/images/Subnets/s2.png)
4. Click **Create subnet**
![endpoint](/images/Subnets/s3.png)

#### Tạo Private Subnet
1. Lặp lại các bước 1 -> 3 phía trên
2. Chọn Subnet vừa tạo -> Chọn **Route table**
![endpoint](/images/Subnets/s4.png)
3. Chọn Route table tương ứng với private
4. Click **Save**
![endpoint](/images/Subnets/s5.png)