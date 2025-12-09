---
title: "Sự kiện 2"
date: "2025-11-29"
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---


# Báo cáo tổng hợp: “AWS Well-Architected Security Pillar”

### Mục tiêu sự kiện

- Đi sâu vào 5 trụ cột cốt lõi của Bảo mật AWS: IAM, Phát hiện, Bảo vệ Hạ tầng, Bảo vệ Dữ liệu và Ứng phó Sự cố.
- Hiểu các nguyên tắc bảo mật hiện đại: Zero Trust, Đặc quyền tối thiểu (Least Privilege) và Phòng thủ theo chiều sâu (Defense in Depth).
- Học cách tự động hóa kiểm tra bảo mật và phản hồi sự cố bằng các công cụ AWS native.
- Nhận diện các mối đe dọa đám mây phổ biến tại thị trường Việt Nam và cách giảm thiểu.

### Điểm nổi bật chính

#### Mở đầu & Nền tảng Bảo mật

- **Nguyên tắc cốt lõi**: Chuyển từ bảo mật chu vi sang **Phòng thủ theo chiều sâu**, kiến trúc **Zero Trust**, và thực thi nghiêm ngặt **Đặc quyền tối thiểu**.
- **Mô hình Trách nhiệm Chia sẻ**: Làm rõ những gì AWS bảo mật (của đám mây) so với những gì khách hàng bảo mật (trong đám mây).
- **Bối cảnh địa phương**: Thảo luận về các mối đe dọa an ninh hàng đầu nhắm vào môi trường cloud tại Việt Nam.

#### Pillar 1: Quản lý Định danh & Truy cập (IAM) 

- **Kiến trúc IAM hiện đại**: Chuyển từ thông tin xác thực dài hạn (IAM Users) sang thông tin xác thực tạm thời (IAM Roles).
- **Quản trị**: Sử dụng **IAM Identity Center** cho SSO và quản lý tập trung.
- **Kiểm soát**: Triển khai **SCPs** và ranh giới quyền hạn (permission boundaries) cho môi trường multi-account.
- **Best Practices**: Bắt buộc dùng MFA, xoay vòng credential định kỳ và dùng **Access Analyzer** để xác thực chính sách.

#### Pillar 2: Phát hiện & Giám sát liên tục

- **Chiến lược ghi nhật ký**: Tiếp cận theo hướng "Log everything" sử dụng **CloudTrail** (cấp tổ chức), **VPC Flow Logs**, và log của ALB/S3.
- **Phát hiện mối đe dọa**: Sử dụng **Amazon GuardDuty** để phát hiện mối đe dọa thông minh.
- **Tập trung hóa**: Tổng hợp các phát hiện trong **AWS Security Hub**.
- **Detection-as-Code**: Tự động hóa cảnh báo bằng **Amazon EventBridge**.

#### Pillar 3: Bảo vệ Hạ tầng

- **An ninh mạng**: Phân đoạn VPC chặt chẽ và phân biệt rõ ràng giữa subnet private và public.
- **Tường lửa**: Hiểu về phòng thủ nhiều lớp sử dụng **WAF** (Web Application Firewall), **AWS Shield** (DDoS), và **Network Firewall**.
- **Kiểm soát truy cập**: Phân biệt giữa tường lửa Stateful (Security Groups) và Stateless (NACLs).

#### Pillar 4: Bảo vệ Dữ liệu

- **Chiến lược mã hóa**:
    - **At-rest (lưu trữ)**: Mã hóa S3 buckets, EBS volumes, RDS, và DynamoDB.
    - **In-transit (truyền tải)**: Thực thi TLS/SSL.
- **Quản lý khóa**: Quản lý khóa qua **AWS KMS**, tập trung vào chính sách cấp quyền (grants) và xoay vòng khóa.
- **Quản lý bí mật**: Loại bỏ credential cứng trong code bằng cách sử dụng **Secrets Manager** và **Systems Manager Parameter Store**.

#### Pillar 5: Ứng phó Sự cố (IR)

- **Vòng đời IR**: Chuẩn bị -> Phát hiện & Phân tích -> Ngăn chặn, Loại bỏ & Khôi phục -> Hoạt động sau sự cố.
- **Tự động hóa**: Sử dụng **AWS Lambda** và **Step Functions** để tự động khắc phục (ví dụ: cách ly EC2 bị nhiễm mã độc).
- **Playbooks**: Đi qua các kịch bản phản ứng tiêu chuẩn như "Lộ khóa IAM", "Lộ lọt S3 Public", và "Phát hiện mã độc".

### Bài học chính

#### Định danh là Chu vi bảo mật mới

- Quản lý định danh là tuyến phòng thủ quan trọng nhất. Access key dài hạn là rủi ro lớn; việc sử dụng IAM Roles và SSO là bắt buộc đối với kiến trúc hiện đại.

#### Khả năng quan sát là Tối quan trọng

- Bạn không thể bảo vệ những gì bạn không thấy. Bật ghi log tập trung (CloudTrail, Config) và phát hiện mối đe dọa (GuardDuty) là bước đầu tiên trong mọi chiến lược bảo mật.

#### Tự động hóa Bảo mật

- Con người thì chậm chạp; tấn công thì nhanh chóng. Các phản ứng bảo mật (khóa user, chặn IP) nên được tự động hóa qua code (Lambda/EventBridge) bất cứ khi nào có thể.

### Áp dụng vào công việc

- **Kiểm tra Chính sách IAM**: Rà soát quyền hạn hiện tại để đảm bảo "Least Privilege" và xóa các IAM User không sử dụng.
- **Bật GuardDuty**: Kích hoạt GuardDuty ở region chính để phát hiện bất thường ngay lập tức.
- **Mã hóa Dữ liệu**: Đảm bảo tất cả S3 bucket và EBS volume mới đều được bật mã hóa mặc định qua KMS.
- **Xây dựng IR Playbooks**: Soạn thảo kế hoạch Ứng phó sự cố cơ bản cho kịch bản "S3 bị lộ public" và "Lộ lọt thông tin xác thực".

### Trải nghiệm sự kiện

Buổi workshop **"AWS Well-Architected Security Pillar"** là một phiên làm việc buổi sáng chuyên sâu và tập trung cao độ. Nó cung cấp một cách tiếp cận có cấu trúc về bảo mật, điều thường bị bỏ qua trong các chu kỳ phát triển vội vã.

#### Hiểu sâu về "Defense in Depth"
- Phần về **Bảo vệ Hạ tầng** đã làm rõ cách phân lớp các kiểm soát bảo mật (WAF -> NACL -> SG) để nếu một lớp thất bại, các lớp khác vẫn hoạt động.

#### Tập trung thực tế vào Tự động hóa
- Việc xem **"Mini Demo"** về xác thực chính sách IAM và mô phỏng truy cập rất hữu ích. Nó cho thấy bảo mật có thể được kiểm thử giống như code phần mềm.
- Phần **Ứng phó Sự cố** đã thay đổi quan điểm của em: thay vì thức dậy lúc 3 giờ sáng để sửa lỗi thủ công, chúng ta có thể viết Lambda function để khoanh vùng mối đe dọa tự động.

#### Bối cảnh 
- Việc nghe về **các sai lầm phổ biến tại doanh nghiệp Việt Nam** giúp em liên hệ các khái niệm lý thuyết với những rủi ro thực tế mà doanh nghiệp chúng em đối mặt hàng ngày.

