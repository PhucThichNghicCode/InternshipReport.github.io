---
title : "Tạo Route Table và Internet Gateway"
date :  "2025-11-05" 
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---
{{% notice note %}}
Route Table là một tập hợp các quy tắc (routes) được router sử dụng để xác định đường đi tốt nhất cho các gói dữ liệu tới đích.
{{% /notice %}}

1. Mở [Amazon VPC console](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#Home:)
2. Chọn **Route tables**, sau đó nhấp **Create route table**
![endpoint](/images/VPC/route1.png)
3. Trong màn hình Create route table:
   1. Nhập tên cho Route table
   2. Chọn VPC đã tạo
   3. Sau đó nhấp **Create route table**
![endpoint](/images/VPC/route2.png)
4. Trong bảng Route table, nhấp vào Route table vừa tạo
![endpoint](/images/VPC/route3.png)
5. Chọn **Routes** ở thanh điều hướng -> nhấp **Edit routes**
![endpoint](/images/VPC/route4.png)
6. Ở màn hình Edit routes -> chọn Target là `local` -> **Save changes**
![endpoint](/images/VPC/route5.png)
7. Chọn **Internet Gateway** trên thanh điều hướng bên trái -> nhấp **Create internet gateway**
![endpoint](/images/VPC/igw1.png)
8. Trong Create Internet Gateway:
   - Nhập tên cho Internet Gateway
   - Nhấp **Create internet gateway**
![endpoint](/images/VPC/igw2.png)
9. Quay lại Route table -> Thực hiện tạo route table giống bước 3
   - Chọn Route table này -> Edit routes
   - Ở Target chọn Internet Gateway vừa tạo
   - Sau đó nhấp **Save changes**
![endpoint](/images/VPC/igw3.png)
