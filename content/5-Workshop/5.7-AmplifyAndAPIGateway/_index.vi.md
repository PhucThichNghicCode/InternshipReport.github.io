---
title : "Tạo Amplify và API Gateway"
date : "2025-11-05"
weight : 6
chapter : false
pre : " <b> 5.7 </b> "
---

#### AWS Amplify
- Tạo Amplify để triển khai Frontend
1. Mở trang [Amazon Amplify](https://ap-southeast-1.console.aws.amazon.com/amplify/home?region=ap-southeast-1#)
2. Nhấp **Create new app**
![overview](/images/Amplify/am1.png)
3. Trong màn hình tạo, chọn **GitLab**, sau đó nhấp **Next**
![overview](/images/Amplify/am2.png)
4. Ở bước Add repository and branch, đăng nhập vào GitLab, sau đó chọn repository và branch cần deploy
5. Sau đó nhấp **Next**
![overview](/images/Amplify/am3.png)
![overview](/images/Amplify/am4.png)
6. Cấu hình format theo loại mã nguồn frontend của bạn, sau đó nhấp **Next**
![overview](/images/Amplify/am5.png)
7. Kiểm tra cấu hình và nhấp **Save and deploy**
![overview](/images/Amplify/am6.png)
- Sau bước này chờ khoảng 3–5 phút để deploy và bạn có thể truy cập ứng dụng từ internet

#### API Gateway
- API Gateway là dịch vụ trung gian chuyển tiếp HTTP/HTTPS giữa Amplify (Frontend) và Fargate (Backend)
1. Mở trang [Amazon API Gateway](https://ap-southeast-1.console.aws.amazon.com/apigateway/main/welcome?region=ap-southeast-1)
2. Nhấp **Create API**
![overview](/images/Amplify/api1.png)
3. Chọn **REST API** và nhấp **Build**
![overview](/images/Amplify/api2.png)
4. Trong màn hình tạo, chọn **API details** là New API
5. Điền tên API
6. Chọn API endpoint type là **Regional**
7. Chọn Security policy là **SecurityPolicy_TLS13_1_2_2021_06**
8. Sau đó nhấp **Create API**
![overview](/images/Amplify/api3.png)
9. Ở thanh điều hướng bên trái, chọn **APIs** và mở API Gateway vừa tạo
![overview](/images/Amplify/api8.png)
10. Ở giao diện API Gateway chọn **Create Resource**, điền Resource name và nhấp **Create resource**
![overview](/images/Amplify/api6.png)
![overview](/images/Amplify/api7.png)
11. Ở resource console, nhấp **Create method**
![overview](/images/Amplify/api9.png)
12. Trong Method details chọn **ANY**, và Integration type là **HTTP Proxy**
13. Chọn HTTP method là **ANY**, và Endpoint URL là endpoint của ALB
14. Sau đó kéo xuống cuối trang và nhấp **Create method**
![overview](/images/Amplify/api10.png)
15. Trong resource proxy, tạo method
![overview](/images/Amplify/api12.png)
16. Trong màn hình tạo, chọn Integration type là **Mock** và Method type là **OPTIONS**
17. Sau đó nhấp **Create method**

- Hoàn tất cấu hình API Gateway cho dự án
