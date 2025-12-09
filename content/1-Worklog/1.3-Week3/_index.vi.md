---
title: "Worklog Tuần 3"
date: "2025-11-05"
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu Tuần 3:

* Nắm vững các dịch vụ tính toán EC2: Vòng đời, Lưu trữ (EBS/Instance Store), và Tự động mở rộng (Auto Scaling).
* Triển khai kết nối nâng cao sử dụng AWS Transit Gateway.
* Triển khai các giải pháp lưu trữ và phân phối nội dung có khả năng mở rộng (S3 & CloudFront).
* Tìm hiểu các khái niệm nền tảng về AI/ML (NLP, Phân tích cảm xúc) cho dự án cuối khóa.
* Phối hợp với nhóm để thảo luận và chốt ý tưởng cho dự án cuối khóa.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Tạo EC2 Instances trong Subnets <br> - Thực hành tạo Internet Gateway <br> - Tìm hiểu về Transit Gateway Route Tables <br> - Cài đặt Transit Gateway <br> - Kết nối EC2 Instance tới Endpoint                                                                                            | 22/09/2025   | 22/09/2025      | <https://www.youtube.com/@AWSStudyGroup/> <br><br> <https://cloudjourney.awsstudygroup.com/>
| 3   | - Tìm hiểu thêm về EC2 <br>&emsp; + AMI/ Backup/ Key pair <br>&emsp; + Elastic block store <br>&emsp; + Instances store <br>&emsp; + User data & meta data <br>&emsp; + EC2 auto scaling<br>                                            | 23/09/2025   | 23/09/2025      | <https://cloudjourney.awsstudygroup.com/> <br><br> <https://www.youtube.com/@AWSStudyGroup/> |
| 4   | - Triển khai hạ tầng <br> - Tạo Backup plan <br> - Tiến hành kiếm thử khôi phục <br> - Dọn dẹp tài nguyên <br> - Tạo S3 Bucket | 24/09/2025   | 24/09/2025      | <https://cloudjourney.awsstudygroup.com/> <br><br> <https://www.youtube.com/@AWSStudyGroup/> |
| 5   | - Tạo EC2 cho Storage Gateway <br> - Thực hành tạo thử 1 website tĩnh đơn giản <br> - Cấu hình public access block và public objects <br> - Tìm hiểu về AWS CloudFont và thực hành cấu hinh cho CloudFont                | 25/09/2025   | 25/09/2025      | <https://cloudjourney.awsstudygroup.com/> <br><br> <https://www.youtube.com/@AWSStudyGroup/> |
| 6   | - Tìm hiểu Supervised ML & Sentiment Analysis <br> - Natural Language preprocessing <br> - Visualizing tweets and Logistic Regression models <br> - Meeting nhóm để lên ý tưởng cho dự án cuối kì                                                                                       | 26/09/2025   | 26/09/2025      | <https://www.coursera.org/learn/classification-vector-spaces-in-nlp/> |


### Thành tựu Tuần 3:

* Đã quản lý và tối ưu hóa thành công các EC2 Instances:
  * Cấu hình AMIs, Key Pairs, và User Data/Meta Data.
  * Phân biệt được sự khác nhau giữa Elastic Block Store (EBS) và Instance Store.
  * Triển khai EC2 Auto Scaling để đảm bảo tính sẵn sàng cao.

* Xây dựng các cấu trúc mạng phức tạp sử dụng AWS Transit Gateway để kết nối các VPC.

* Thực hiện các hoạt động Khôi phục sau thảm họa và Lưu trữ:
  * Tạo các Backup plans và thực hiện kiểm thử khôi phục thành công.
  * Triển khai một Website tĩnh sử dụng S3 Buckets.
  * Cấu hình các cài đặt Truy cập công khai và Object permissions.

* Tối ưu hóa hiệu suất phân phối nội dung: 
  * Tích hợp AWS CloudFront (CDN) với S3 để giảm độ trễ và cải thiện tốc độ truy cập.

* Bước đầu tiếp cận với Machine Learning (NLP):
  * Hiểu các khái niệm về Học máy có giám sát (Supervised ML) & Phân tích cảm xúc.
  * Thực hành tiền xử lý Ngôn ngữ tự nhiên và trực quan hóa Hồi quy Logistic.
  * Xác định khái niệm ban đầu cho dự án cuối khóa.


