---
title: "Bản đề xuất"
date: "2025-11-05"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# APEX-EV Electric Vehicle Service Platform 
## Nền tảng quản lí và bảo dưỡng OTO điện APEX-EV

### 1. Tóm tắt điều hành  
ApexEV là một nền tảng quản lý toàn diện, được thiết kế nhằm số hóa và tối ưu hóa quy trình bảo dưỡng tại các trung tâm dịch vụ ô tô điện. Hệ thống quản lý tập trung toàn bộ vòng đời dịch vụ—từ đặt hẹn, tiếp nhận, sửa chữa, đến thanh toán—giúp loại bỏ các thao tác thủ công và nâng cao hiệu quả hoạt động. Bằng việc tận dụng sức mạnh của kiến trúc Hybrid (Lai) của AWS, kết hợp tính linh hoạt của container trên Amazon ECS Fargate và các dịch vụ serverless, ApexEV mang đến khả năng giám sát theo thời gian thực, bảo mật cao, và gia tăng sự hài lòng của khách hàng. Nền tảng được phân quyền truy cập an toàn cho năm vai trò người dùng chính thông qua Amazon Cognito.  

### 2. Tuyên bố vấn đề  
*Vấn đề hiện tại*  
 Quy trình vận hành hiện tại còn phụ thuộc nhiều vào các phương pháp thủ công, dẫn đến hiệu suất làm việc không hiệu quả, dữ liệu rời rạc, và trải nghiệm khách hàng bị động.

*Giải pháp*  
Nền tảng sử dụng một kiến trúc bảo mật đa lớp. Giao diện (Frontend) React.js được triển khai trên AWS Amplify và phân phối toàn cầu qua Amazon CloudFront, được bảo vệ bởi AWS WAF (Tường lửa).

Toàn bộ logic nghiệp vụ chính (Backend) là một ứng dụng Spring Boot chạy trên Amazon ECS Fargate, được đặt an toàn trong Private Subnet của VPC (Mạng riêng). Amazon API Gateway đóng vai trò là cổng vào, nhận yêu cầu từ frontend (đã được xác thực bằng Amazon Cognito) và chuyển tiếp đến Application Load Balancer (ALB) trước khi vào ECS.
Để đạt hiệu suất cao, hệ thống sử dụng Amazon ElastiCache (Redis) làm bộ đệm (cache) và Amazon RDS (SQL Server) làm cơ sở dữ liệu quan hệ chính. Dữ liệu đa phương tiện (ảnh/video) được lưu trữ trên Amazon S3.

Các tác vụ bất đồng bộ (như gửi email) được xử lý qua Amazon SNS -> AWS Lambda -> Amazon SES. Các kết nối ra Internet từ backend (ví dụ: gọi API thanh toán, gọi API Gemini) được thực hiện an toàn qua NAT Gateway.  

*Lợi ích và hoàn vốn đầu tư (ROI)*  
Giải pháp kiến trúc trên AWS không chỉ số hóa toàn bộ quy trình mà còn tạo ra lợi thế cạnh tranh vượt trội về hiệu suất, an ninh và khả năng mở rộng. Nền tảng đảm bảo trải nghiệm người dùng tức thì và mượt mà nhờ hệ thống phân phối toàn cầu CloudFront và bộ đệm ElastiCache, trong khi dữ liệu được bảo vệ an toàn qua nhiều lớp với AWS WAF và mạng riêng ảo VPC.

Việc vận hành trên Amazon ECS Fargate giúp tự động hóa và loại bỏ gánh nặng quản lý hạ tầng, cho phép nhân viên tập trung tối đa vào chuyên môn và dịch vụ khách hàng. Với chi phí vận hành ước tính khoảng $50 - $85 USD/tháng, khoản đầu tư này nhanh chóng được bù đắp bằng việc giảm thiểu rủi ro an ninh, tăng năng suất lao động và cải thiện doanh thu nhờ sự hài lòng của khách hàng. Do đó, thời gian hoàn vốn đầu tư (ROI) dự kiến chỉ từ 8-15 tháng, sau đó nền tảng sẽ trở thành một động lực tăng trưởng bền vững cho doanh nghiệp.

### 3. Kiến trúc giải pháp  
Nền tảng quản lý bảo dưỡng ô tô điện APEX-EV áp dụng một kiến trúc bảo mật đa lớp, hiệu suất cao trên AWS. Giao diện người dùng được xây dựng bằng React.js, triển khai qua AWS Amplify và phân phối toàn cầu bởi Amazon CloudFront, đồng thời được bảo vệ bởi tường lửa ứng dụng web AWS WAF. Toàn bộ logic nghiệp vụ chính là một ứng dụng Spring Boot chạy trên Amazon ECS Fargate, được đặt an toàn trong một mạng riêng (Private Subnet). Amazon API Gateway đóng vai trò cổng giao tiếp, xác thực yêu cầu qua Amazon Cognito trước khi chuyển đến Application Load Balancer và vào ứng dụng. Để tối ưu tốc độ, nền tảng sử dụng Amazon ElastiCache (Redis) làm bộ đệm, Amazon RDS (SQL Server) cho cơ sở dữ liệu chính, và Amazon S3 để lưu trữ các tệp đa phương tiện. Các tác vụ bất đồng bộ như gửi thông báo được xử lý hiệu quả qua chuỗi dịch vụ Amazon SNS, Lambda và SES.  

![APEX-EV Platform Architecture](/images/2-Proposal/APEX-EV.jpg)

*Dịch vụ AWS sử dụng*  
- *Amazon ECS Fargate*: Chạy ứng dụng backend (Spring Boot) dưới dạng container mà không cần quản lý máy chủ.  
- *AWS Lambda*: Xử lý các tác vụ bất đồng bộ, ví dụ như xử lý thông báo từ SNS để gửi email.
- *Amazon API Gateway*: Giao tiếp với ứng dụng web.  
- *Amazon S3*: Lưu trữ dữ liệu thô (data lake) và dữ liệu đã xử lý (2 bucket).  
- *Amazon RDS*: Cung cấp cơ sở dữ liệu quan hệ (SQL Server) để lưu trữ dữ liệu có cấu trúc (hồ sơ, lịch hẹn, phụ tùng). 
- *AWS Amplify*: Lưu trữ giao diện web Next.js.  
- *Amazon Cognito*: Quản lý quyền truy cập cho người dùng.  
- *Amazon ElastiCache*: Cung cấp bộ đệm (cache) trong bộ nhớ (Redis) để tăng tốc độ truy xuất dữ liệu và cải thiện hiệu suất.  
- *NAT Gateway*: Cho phép các tài nguyên trong mạng riêng (private subnet) kết nối ra Internet một cách an toàn.
- *AWS WAF*: Tường lửa ứng dụng web, bảo vệ nền tảng khỏi các cuộc tấn công mạng phổ biến. 
- *AWS SNS*: QDịch vụ thông báo, dùng để khởi tạo các quy trình bất đồng bộ (ví dụ: gửi thông báo để kích hoạt Lambda). 
- *Route 53*: Dịch vụ DNS, chịu trách nhiệm định tuyến tên miền của bạn đến ứng dụng.
- *AWS SES*: Dịch vụ gửi email, thực hiện việc gửi các thông báo và báo giá đến khách hàng.
- *AWS ECR*: Kho lưu trữ các image container (Docker) của ứng dụng để triển khai trên ECS Fargate.
- *AWS CloudFont*: Mạng lưới phân phối nội dung (CDN) giúp tăng tốc độ tải và phân phối giao diện web (frontend) đến người dùng trên toàn cầu với độ trễ thấp.

*Thiết kế thành phần*  
- *Tiếp nhận yêu cầu*: Amazon API Gateway đóng vai trò là cổng vào, tiếp nhận và điều hướng toàn bộ yêu cầu từ giao diện web sau khi được xác thực.  
- *Lưu trữ dữ liệu*: Dữ liệu quan hệ (Hồ sơ, Lịch hẹn, Phụ tùng) được lưu trên Amazon RDS; các tệp đa phương tiện (ảnh/video bảo dưỡng) được lưu trữ trên Amazon S3. 
- *Xử lý nghiệp vụ*: Toàn bộ logic chính được xử lý bởi ứng dụng Spring Boot chạy trên Amazon ECS Fargate; các tác vụ bất đồng bộ như gửi email được xử lý bởi AWS Lambda.  
- *Giao diện web*: AWS Amplify triển khai và lưu trữ ứng dụng React.js. Amazon CloudFront phân phối nội dung toàn cầu để tăng tốc độ và được bảo vệ bởi AWS WAF. 
- *Quản lý người dùng*: Amazon Cognito quản lý toàn bộ quá trình xác thực, phân quyền chi tiết và bảo mật tài khoản cho tất cả người dùng. 

### 4. Triển khai kỹ thuật  
*Các giai đoạn triển khai*  
Dự án gồm 2 phần — thiết lập trạm thời tiết biên và xây dựng nền tảng thời tiết — mỗi phần trải qua 4 giai đoạn:  
1. *Nghiên cứu và vẽ kiến trúc*: Nghiên cứu các công nghệ phù hợp (React.js, Spring Boot) và thiết kế kiến trúc hệ thống đa lớp chi tiết trên AWS (1 tháng trước khi bắt đầu).  
2. *Tính toán chi phí và kiểm tra tính khả thi*: Sử dụng AWS Pricing Calculator để ước tính chi phí vận hành cho các dịch vụ cốt lõi như ECS, RDS, WAF và đưa ra phương án khả thi.
3. *Điều chỉnh kiến trúc để tối ưu chi phí/giải pháp*: Tinh chỉnh kiến trúc, lựa chọn cấu hình phù hợp cho ECS Fargate, RDS và ElastiCache để cân bằng giữa hiệu năng và chi phí.
4. *Phát triển, kiểm thử, triển khai*: Lập trình ứng dụng React.js (Frontend), Spring Boot (Backend), triển khai hạ tầng bằng AWS CDK, sau đó kiểm thử toàn diện và đưa vào vận hành. 

*Yêu cầu kỹ thuật*  
- *Giao diện người dùng (Frontend*: Kiến thức thực tế về React.js để xây dựng giao diện người dùng hiện đại. Sử dụng AWS Amplify để tự động hóa quy trình triển khai, Amazon CloudFront để phân phối nội dung toàn cầu với độ trễ thấp, và AWS WAF để bảo vệ ứng dụng khỏi các cuộc tấn công web.  
- *Hệ thống lõi (Backend & Infrastructure)*: KKiến thức chuyên sâu về Java/Spring Boot để phát triển logic nghiệp vụ. Ứng dụng được đóng gói bằng Docker, lưu trữ trên Amazon ECR và chạy trên Amazon ECS Fargate. Yêu cầu hiểu biết về Amazon RDS (SQL Server) cho cơ sở dữ liệu chính, ElastiCache (Redis) để caching. Sử dụng thành thạo AWS CDK để định nghĩa và triển khai toàn bộ hạ tầng một cách tự động, bao gồm VPC, API Gateway, Application Load Balancer và các quy tắc bảo mật. Quản lý xác thực và phân quyền người dùng chi tiết qua Amazon Cognito. 

### 5. Lộ trình & Mốc triển khai  
*Các giai đoạn triển khai*   
1. *Giai đoạn 1 (Tuần 1-2): Thiết kế và Nền tảng*:
- Phân tích & Thiết kế kiến trúc AWS chi tiết. Thiết kế CSDL (Schema) và API Endpoints. 
- Cấu hình môi trường AWS (VPC, Subnets, IAM, Cognito). 
- Thiết lập CI/CD cơ bản (GitLab -> Amplify, GitLab -> ECR).
2. *Giai đoạn 2 (Tuần 3-4): Phát triển Luồng Dịch vụ Cốt lõi*: 
- Phát triển luồng Khách hàng (Đặt lịch, Hồ sơ cá nhân).
- Phát triển luồng Cố vấn Dịch vụ (Quản lý lịch hẹn, Tạo báo giá).
- Phát triển luồng Kỹ thuật viên (Checklist bảo dưỡng, Cập nhật tiến độ).
3. *Giai đoạn 3 (Tuần 5-6): Phát triển chức năng Quản trị & Nâng cao*: 
- Xây dựng Module Quản lý (Xem báo cáo, Quản lý kho, Nhân sự).
- Xây dựng Module Admin (Phân quyền, Quản lý dịch vụ).
- Tích hợp AI Chatbot (gọi API Gemini qua NAT Gateway) và luồng thông báo (SNS/SES).
4. *Giai đoạn 4 (Tuần 7-8): Kiểm thử, Tối ưu và Vận hành*:  
- Kiểm thử Chấp nhận Người dùng (UAT) nội bộ để tìm lỗi và thu thập phản hồi.
- Tập trung sửa lỗi và tinh chỉnh theo phản hồi.
- Tối ưu hóa bảo mật (WAF Rules, IAM policies) và cấu hình giám sát (CloudWatch Dashboard/Alarms).
- Triển khai chính thức (Go-live).

### 6. Ước tính ngân sách  
*Chi phí hạ tầng*  
- AWS Lambda: 0,00 USD/tháng (1.000 request, 512 MB lưu trữ).  
- S3 Standard: 0,15 USD/tháng (6 GB, 2.100 request, 1 GB quét).  
- Amazon RDS: 0,00 USD/tháng (Miễn phí 750 giờ/tháng trong 12 tháng).  
- AWS Amplify: 0,35 USD/tháng (256 MB, request 500 ms).  
- Amazon API Gateway: 0,01 USD/tháng (2.000 request).  
- Amazon ElastiCache: 0,00 USD/tháng (Miễn phí 750 giờ/tháng trong 12 tháng). 
- Amazon ECS Fargate: ~10,00 USD/tháng 1 task (0.5 vCPU, 1GB RAM). 
- Application Load Balancer: ~16,2 USD/tháng.  
- NAT Gateway: ~32,45 USD/tháng (Lối ra Internet cho ECS/AI).
- AWS WAF: ~6,00 USD/tháng (Tường lửa).
- Dịch vụ Serverless: ~5,00 USD/tháng, gần như hoàn toàn miễn phí trong Free Tier (trừ Route 53 và S3 nếu vượt 5GB).

*Tổng*: ~69.95 USD/tháng.

### 7. Đánh giá rủi ro  
*Ma trận rủi ro*  
- Hệ thống ngừng hoạt động: Ảnh hưởng cao, xác suất thấp.
- Vi phạm an ninh/mất dữ liệu: Ảnh hưởng cao, xác suất trung bình.
- Vượt chi phí vận hành: Ảnh hưởng trung bình, xác suất trung bình. 

*Chiến lược giảm thiểu*  
- Hệ thống: Triển khai hạ tầng trên nhiều Vùng sẵn sàng (Multi-AZ) cho RDS và ECS Fargate. Sử dụng Application Load Balancer để tự động phân phối tải và phục hồi.
- An ninh: Sử dụng AWS WAF để lọc các yêu cầu độc hại. Phân quyền chặt chẽ với Amazon Cognito và áp dụng nguyên tắc đặc quyền tối thiểu. Backend được đặt trong mạng riêng biệt (Private Subnet).
- Chi phí: Sử dụng AWS Budgets để thiết lập cảnh báo khi chi phí vượt ngưỡng. Thường xuyên theo dõi và tối ưu hóa tài nguyên (right-sizing) để tránh lãng phí.

*Kế hoạch dự phòng*  
- Kích hoạt quy trình khắc phục sự cố, sử dụng các bản sao lưu của RDS để khôi phục dữ liệu.
- Triển khai một trang thông báo bảo trì tĩnh trên S3/CloudFront để thông báo cho người dùng trong trường hợp hệ thống chính gặp sự cố kéo dài.
 

### 8. Kết quả kỳ vọng  
*Cải tiến kỹ thuật*: Một hệ thống API-first tập trung, thay thế hoàn toàn quy trình thủ công rời rạc. Kiến trúc an toàn, có khả năng mở rộng (nhờ ECS và RDS). Dữ liệu được quản lý tập trung, an toàn và sẵn sàng cho phân tích.
  
*Giá trị dài hạn*: Xây dựng một tài sản dữ liệu có giá trị, làm cơ sở cho các phân tích kinh doanh và quyết định chiến lược trong tương lai. Tạo nền tảng công nghệ vững chắc để dễ dàng mở rộng các tính năng mới (VD: Phân tích AI dự đoán hỏng hóc). Tăng hiệu suất làm việc của nhân viên và tăng tỷ lệ khách hàng quay lại nhờ trải nghiệm dịch vụ minh bạch, vượt trội.
