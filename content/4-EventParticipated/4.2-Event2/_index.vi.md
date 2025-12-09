---
title: "Sự kiện 1"
date: "2025-11-15"
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Báo cáo tổng hợp: “DevOps trên AWS”

### Mục tiêu sự kiện

- Hiểu rõ tư duy DevOps, văn hóa và các chỉ số hiệu suất quan trọng (DORA).
- Làm chủ chuỗi công cụ CI/CD của AWS để chuyển giao phần mềm tự động.
- Nắm vững nguyên lý Infrastructure as Code (IaC) sử dụng CloudFormation và CDK.
- Khám phá các chiến lược container hóa và các thực hành tốt nhất về giám sát (observability) trên AWS.


### Điểm nổi bật chính

#### Văn hóa DevOps và Các chỉ số quan trọng

- **Thay đổi tư duy**: Chuyển dịch từ việc làm việc biệt lập (silos) sang trách nhiệm chia sẻ giữa đội ngũ Phát triển (Dev) và Vận hành (Ops).
- **Các chỉ số cốt lõi (DORA)**: Tập trung vào Tần suất triển khai (Deployment Frequency), Thời gian dẫn (Lead Time for Changes), Thời gian trung bình để khôi phục (MTTR), và Tỷ lệ lỗi khi thay đổi (Change Failure Rate).
- **Lợi ích**: Đổi mới nhanh hơn, độ tin cậy cao hơn và cải thiện sự cộng tác.

#### Dịch vụ AWS DevOps – Quy trình CI/CD 

Chúng em đã khám phá quy trình tự động hóa toàn diện, chuyển từ triển khai thủ công sang điều phối tự động:

- **Quản lý mã nguồn (Source Control)**: Sử dụng **AWS CodeCommit** và áp dụng các chiến lược Git như GitFlow hoặc Trunk-based.
- **Xây dựng & Kiểm thử (Build & Test)**: Sử dụng **AWS CodeBuild** để biên dịch mã nguồn, chạy kiểm thử và đóng gói phần mềm.
- **Chiến lược triển khai**: Thực hiện **AWS CodeDeploy** với các cập nhật Blue/Green, Canary, và Rolling để giảm thiểu thời gian chết (downtime).
- **Điều phối (Orchestration)**: Kết nối tất cả các khâu lại với nhau bằng **AWS CodePipeline**.

#### Cơ sở hạ tầng dưới dạng mã (IaC)

Chuyển đổi từ việc click chuột thủ công trên console sang định nghĩa hạ tầng bằng mã:

- **AWS CloudFormation**: Sử dụng template JSON/YAML để định nghĩa tài nguyên, quản lý các stack và phát hiện sai lệch hạ tầng (drift detection).
- **AWS CDK (Cloud Development Kit)**: Định nghĩa tài nguyên đám mây bằng các ngôn ngữ lập trình quen thuộc (Python, TypeScript, Java) để tạo ra các cấu trúc tái sử dụng được.
- **Lựa chọn công cụ**: Thảo luận khi nào nên dùng template khai báo (CloudFormation) so với mã mệnh lệnh (CDK).

#### Dịch vụ Container trên AWS 
- **Căn bản về Docker**: Đóng gói ứng dụng thành các container nhẹ và linh hoạt.
- **Registry**: Sử dụng **Amazon ECR** để lưu trữ image an toàn và quét lỗ hổng bảo mật.
- **Điều phối (Orchestration)**: So sánh giữa **Amazon ECS** (đơn giản, thuần AWS) và **Amazon EKS** (dịch vụ Kubernetes được quản lý) để mở rộng microservices.
- **App Runner**: Dịch vụ được quản lý hoàn toàn dành cho các nhà phát triển muốn triển khai container mà không cần quản lý bộ điều phối.

#### Giám sát & Khả năng quan sát (Observability) 

- **CloudWatch**: Trung tâm của các chỉ số (metrics), nhật ký (logs), cảnh báo (alarms) và bảng điều khiển vận hành.
- **AWS X-Ray**: Cung cấp khả năng truy vết phân tán (distributed tracing) để xác định các nút thắt hiệu năng trong kiến trúc microservices.
- **Best Practices**: Thiết lập các cảnh báo khả thi và quy trình trực chiến (on-call).

### Bài học chính

#### Tư duy DevOps

- **Văn hóa hơn Công cụ**: Công cụ là thiết yếu, nhưng văn hóa cộng tác và sự an toàn tâm lý là điều kiện tiên quyết để DevOps thành công.
- **Đo lường mọi thứ**: Bạn không thể cải thiện những gì bạn không đo lường. Các chỉ số DORA là tiêu chuẩn vàng cho tốc độ và sự ổn định.

#### Triển khai kỹ thuật

- **Tự động hóa mọi thứ**: Từ cung cấp hạ tầng (IaC) đến triển khai mã (CI/CD), các quy trình thủ công cần được loại bỏ.
- **Cơ sở hạ tầng bất biến (Immutable Infrastructure)**: Coi máy chủ như gia súc thay vì thú cưng (cattle vs pets), sử dụng container và cung cấp tự động.
- **Shift Left (Dịch chuyển sang trái)**: Tích hợp kiểm thử và bảo mật ngay từ đầu quy trình (CodeBuild) thay vì đợi đến cuối.

#### Vận hành hiện đại

- **Observability là bắt buộc**: Chỉ biết hệ thống "bị sập" là chưa đủ; bạn phải biết "tại sao" thông qua tracing và logs.
- **Triển khai lũy tiến (Progressive Delivery)**: Sử dụng Canary hoặc Blue/Green để giảm thiểu phạm vi ảnh hưởng (blast radius) khi cập nhật.

### Áp dụng vào công việc

- **Triển khai CI/CD Pipeline**: Chuyển quy trình triển khai thủ công hiện tại sang AWS CodePipeline.
- **Áp dụng IaC**: Bắt đầu định nghĩa hạ tầng cốt lõi bằng AWS CDK để kiểm soát phiên bản và khả năng tái lập tốt hơn.
- **Container hóa Microservices**: Tái cấu trúc (Refactor) các dịch vụ cũ thành Docker container và triển khai qua ECS để mở rộng tốt hơn.
- **Tăng cường Observability**: Tích hợp AWS X-Ray vào ứng dụng để truy vết các request qua các dịch vụ.
- **Thiết lập chỉ số DORA**: Bắt đầu theo dõi tần suất triển khai và MTTR để đánh giá hiệu suất của nhóm.

### Trải nghiệm sự kiện

Tham dự hội thảo **“DevOps on AWS”** là một trải nghiệm mang tính chuyển đổi, giúp em thu hẹp khoảng cách giữa lý thuyết DevOps và việc triển khai thực tế trên AWS. Những trải nghiệm chính bao gồm:

#### Hòa mình vào Văn hóa DevOps
- Phiên buổi sáng đã làm rõ rằng DevOps không chỉ là một chức danh công việc mà là một phương pháp luận.
- Hiểu về **chỉ số DORA** đã cho em một khung cụ thể để đánh giá hiệu quả của chính nhóm mình.

#### Thực hành xây dựng Pipeline
- Việc đi qua một bản **demo CI/CD pipeline** đầy đủ là điểm nhấn. Chứng kiến việc commit code kích hoạt quá trình build, chạy test và triển khai qua chiến lược **Blue/Green** đã chứng minh sức mạnh của tự động hóa.
- em đã hiểu rõ hơn về các chiến lược Git, cụ thể là khi nào nên dùng **GitFlow** so với **Trunk-based** cho quy mô nhóm hiện tại.

#### Làm sáng tỏ Infrastructure as Code
- Sự so sánh giữa **CloudFormation** và **CDK** thực sự mở mang tầm mắt. Mặc dù em đã hiểu về template, nhưng việc thấy sức mạnh của CDK trong việc sử dụng logic và vòng lặp để tạo hạ tầng đã thay đổi cách em lên kế hoạch cung cấp tài nguyên trong tương lai.

#### Đi sâu vào Container và Observability
- Phiên thảo luận về **ECS so với EKS** đã giúp giải quyết các tranh luận nội bộ về việc nên chọn bộ điều phối nào.
- Việc thấy **AWS X-Ray** trực quan hóa một truy vết phân tán làm em nhận ra hệ thống production hiện tại của chúng em đang thiếu khả năng quan sát đến mức nào.

#### Kết nối và Hỏi đáp
- Phiên Q&A về **lộ trình sự nghiệp** và **chứng chỉ** đã cung cấp một bản đồ rõ ràng cho sự phát triển chuyên môn.
- Thảo luận về việc **quản lý sự cố** (postmortems) với