---
title: "Worklog Tuần 10"
date: "2025-11-05"
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---


### Mục tiêu Tuần 10:

* Nắm vững các kỹ thuật NLP nâng cao: Kiến trúc BERT, Fine-tuning, và các chiến lược Multi-Task Training.
* Hoàn thiện giao diện sản phẩm (UI) và thực hiện kiểm thử hệ thống toàn diện.
* Tối ưu hóa kiến trúc hệ thống bằng cách chuyển đổi tích hợp AI sang mô hình Serverless.
* Triển khai tích hợp trực tiếp Frontend-to-Bedrock sử dụng AWS Lambda.


### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Học về Bidirectional Encoder Representations from Transformers <br> - Cách fine tune lại mô hình BERT  <br> - Tìm hiểu về Multi-Task Training Strategy <br> - Làm lab code về fine tune lại model BERT dựa trên dữ liệu có sẵn                                                                                         | 10/11/2025   | 10/11/2025      | <https://www.coursera.org/learn/attention-models-in-nlp/>
| 3   | - Hoàn thiện giao diện người dùng <br> - Các chức năng cơ bản <br> - Tiến hành kiêmr thử sản phẩm | 11/01/2025   | 11/01/2025      |  |
| 4   | - Meeting thảo luận về 1 số tính năng sẽ thêm vào - Tối ưu lại hệ thống <br> | 12/11/2025   | 12/11/2025      |  |
| 5   | - Tối ưu hệ thống <br> - Sửa lỗi tích hợp liên quan để Bedrock AI ChatBot | 13/11/2025   | 13/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Viết Lambda Function để tích hợp Bedrock vào Front-end thay vì Back-end - Nghiên cứu cách tích hợp Bedrock vào Front-end của dự án                                                                                         | 14/08/2025   | 14/08/2025      | <https://cloudjourney.awsstudygroup.com/> |


### Thành tựu Tuần 10:

* Nắm vững NLP Nâng cao (BERT):
  * Hiểu kiến trúc **Bidirectional Encoder Representations from Transformers (BERT)**.
  * Áp dụng thành công **Multi-Task Training Strategies**.
  * Hoàn thành bài thực hành về **Fine-tuning BERT** với các tập dữ liệu tùy chỉnh để cải thiện độ chính xác của mô hình.

* Hoàn thiện Sản phẩm:
  * Hoàn thành **User Interface (UI)** và xác minh tất cả các chức năng cốt lõi.
  * Thực hiện giai đoạn kiểm thử đầy đủ để phát hiện và xử lý lỗi (bugs).

* Tái cấu trúc & Tối ưu hóa Kiến trúc: 
  * Chuyển đổi tích hợp AI Chatbot từ cách tiếp cận backend nguyên khối (monolithic) sang **Serverless architecture**.
  * Phát triển các **AWS Lambda functions** để cho phép Frontend giao tiếp trực tiếp với **AWS Bedrock**, cải thiện độ trễ và khả năng mở rộng.
  * Tối ưu hóa hiệu suất tổng thể của hệ thống dựa trên kết quả thảo luận của nhóm.


