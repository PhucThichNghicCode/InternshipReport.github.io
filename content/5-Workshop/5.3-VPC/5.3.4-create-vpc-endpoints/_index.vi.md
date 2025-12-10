---
title : "Tạo VPC Endpoints"
date :  "2025-11-05" 
weight : 2
chapter : false
pre : " <b> 5.3.4 </b> "
---
1. Mở [Amazon VPC console](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#Home:)
2. Chọn **Endpoints** -> nhấp **Create endpoints**
![endpoint](/images/Endpoints/e1.png)
3. Trong màn hình Create:
   - Điền tên cho VPC endpoint
   - Chọn Type là **AWS Services**
   - Tìm kiếm dịch vụ cần thiết
![endpoint](/images/Endpoints/e2.png)
4. Trong dự án này chúng ta tạo 5 VPC Endpoints
   1. VPC Endpoint S3 Gateway (`apexev-s3-gateway`)
      - Trong ô tìm kiếm -> `com.amazonaws.ap-southeast-1.s3`
      - Chọn type **Gateway**
      - Chọn VPC đã tạo
      - Chọn Route table private
      - Policy: Full access
      - Nhấp **Create endpoint**
![endpoint](/images/Endpoints/e3.png)
   2. VPC Endpoint ECR API & DKR (Interface)
      - Trong ô tìm kiếm -> `ecr`
      - Bạn sẽ thấy `ecr.api` và `ecr.dkr` (interface)
      - Thực hiện thao tác hai lần, mỗi lần chọn một loại khác nhau
      - Chọn VPC đã tạo
      - Chọn hai subnet private ở hai AZ khác nhau
      - Chọn `endpoint-sg`
      - Nhấp **Create endpoint**
![endpoint](/images/Endpoints/e4.png)
   3. VPC Endpoint Logs
      - Trong ô tìm kiếm -> `com.amazonaws.ap-southeast-1.logs`
      - Chọn `com.amazonaws.ap-southeast-1.logs`
      - Chọn VPC đã tạo
      - Chọn hai subnet private ở hai AZ khác nhau
      - Chọn `endpoint-sg`
      - Nhấp **Create endpoint**
![endpoint](/images/Endpoints/e5.png)
   4. VPC Endpoint AWS SNS
      - Trong ô tìm kiếm -> `com.amazonaws.ap-southeast-1.sns`
      - Chọn `com.amazonaws.ap-southeast-1.sns`
      - Chọn VPC đã tạo
      - Chọn hai subnet private ở hai AZ khác nhau
      - Chọn `endpoint-sg`
      - Nhấp **Create endpoint**
![endpoint](/images/Endpoints/e6.png)
