---
title: "Sự kiện 3"

date: "2025-11-15"
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Báo cáo tổng hợp: “AWS AI/ML và Generative AI Workshop”

### Mục tiêu sự kiện

- Nắm bắt tổng quan các dịch vụ AWS AI/ML (Amazon SageMaker) và nền tảng AI sinh tạo (Amazon Bedrock).
- Hiểu sâu về phương pháp luận AIDLC (AI-Driven Development) để tăng tốc độ phát triển phần mềm.
- Làm chủ công cụ Kiro IDE và các khái niệm mới như Spec-Driven Development (SDD) và Agent Hooks.
- Học cách tư duy Prompt Engineering hiệu quả và quản lý ngữ cảnh để làm việc với AI.

### Điểm nổi bật chính

#### Dịch vụ AWS AI/ML và Bedrock 

Chương trình đã giới thiệu bức tranh toàn cảnh về khả năng AI của AWS:

- **Amazon SageMaker**: Nền tảng ML end-to-end bao gồm chuẩn bị dữ liệu, huấn luyện, tinh chỉnh và MLOps tích hợp.
- **Amazon Bedrock**: Dịch vụ chủ đạo cho AI Sinh tạo, cho phép lựa chọn các Foundation Models (Claude, Llama, Titan).
- **Các tính năng nâng cao**: Thảo luận về kiến trúc RAG (Retrieval-Augmented Generation), tích hợp Knowledge Base, Bedrock Agents cho quy trình đa bước và Guardrails để lọc nội dung.

#### Phương pháp luận AIDLC (AI-Driven Development) 

Đây là trọng tâm của sự thay đổi cách làm việc, giúp rút ngắn thời gian từ 2 tuần xuống 1.5 ngày:

- **Triết lý Human-Centric**: Con người nắm vai trò xác nhận (validation) và ra quyết định. AI không bao giờ tự động quyết định thay con người.
- **Quy trình 3 giai đoạn**: Inception (Định hình yêu cầu/User Story), Construction (Triển khai Unit), và Operation (Deploy/CICD).
- **Phát triển theo nhóm (Mob Development)**: Đề xuất mô hình làm việc nhóm đa vai trò (BA, Engineer, SA, QA) trên cùng một máy để xác nhận đầu ra ngay lập tức.

#### Công cụ Kiro IDE & Spec-Driven Development 

Khám phá IDE thế hệ mới tích hợp sâu AI (tương tự Coder/VS Code):

- **Spec-Driven Development (SDD)**: Bắt đầu bằng tài liệu đặc tả (spec) thay vì viết code. Kiro tự động tạo các file requirement, design, và task list.
- **Agent Hooks**: Tính năng tự động hóa tác vụ dựa trên sự kiện bằng ngôn ngữ tự nhiên (ví dụ: tự chạy test khi lưu file).
- **Advanced Context Management**: Tự động chạy quy trình AIDLC thông qua file cấu hình `steering`.

#### Quản lý ngữ cảnh (Context Management)

- **Tối ưu hóa đầu vào**: AI hiểu ngữ cảnh ngôn ngữ tốt hơn code thô. Cần rút trích code thành project summary hoặc mô hình miền (domain model).
- **Kiểm soát Session**: Tạo session riêng cho từng tác vụ (Unit of Work) để tránh "ngộ độc ngữ cảnh" (context overload) và giảm thiểu ảo giác (hallucination).

### Bài học chính

#### Tư duy làm việc với AI

- **Lập kế hoạch là bắt buộc**: Khi yêu cầu AI làm việc, bắt buộc phải yêu cầu nó tạo Plan trước. Lặp lại việc tạo Plan cho đến khi chính xác mới tiến hành.
- **Tư duy "Đừng cấm đoán"**: Thay vì bảo AI "đừng làm X", hãy hướng dẫn cụ thể "hãy làm Y". Các câu lệnh phủ định thường kém hiệu quả hơn khẳng định.
- **Vai trò mới của Developer**: Chuyển dịch từ người viết code (coding) sang người giám sát (oversight), xác thực (validation) và ra quyết định.

#### Kỹ thuật Prompting

- **Xác định Persona**: Luôn định nghĩa vai trò rõ ràng cho AI ngay từ đầu.
- **Input/Output rõ ràng**: Chỉ định định dạng đầu ra cụ thể (ví dụ: file Markdown) thay vì để AI lưu trong bộ nhớ tạm.

### Áp dụng vào công việc

- **Áp dụng quy trình AIDLC**: Bắt đầu chia nhỏ dự án thành các giai đoạn Inception, Construction, Operation và thực hành tạo Plan trước khi code.
- **Chuyển đổi công cụ IDE**: Thử nghiệm Kiro IDE hoặc các công cụ hỗ trợ AI tương tự, tận dụng Agent Hooks để tự động hóa quy trình kiểm thử.
- **Tối ưu hóa Context**: Xây dựng th অভ্যাস viết tài liệu tóm tắt kiến trúc và domain model để nạp context cho AI thay vì quăng toàn bộ source code.
- **Thực hành Mob Development**: Tổ chức các phiên làm việc nhóm tập trung để validate kết quả do AI tạo ra ngay lập tức.

### Trải nghiệm sự kiện

Tham dự Workshop **"AWS AI/ML và Generative AI"** đã mang lại một góc nhìn hoàn toàn mới về tương lai của lập trình. Những trải nghiệm đáng nhớ bao gồm:

#### Sự thay đổi về tốc độ và tư duy
- em thực sự ấn tượng với khái niệm **AIDLC**, đặc biệt là khả năng rút ngắn thời gian phát triển đáng kinh ngạc. Nó không chỉ là công cụ nhanh hơn, mà là cách tiếp cận vấn đề hoàn toàn khác.

#### Hình tượng "Chiếc xe tự lái"
- Ví dụ minh họa về việc phát triển phần mềm giống như **điều khiển xe tự lái** thực sự sâu sắc. em nhận ra mình không cần lái từng mét đường, nhưng phải là người cầm bản đồ (Plan) và chịu trách nhiệm đạp phanh (Validation) để đảm bảo an toàn.

#### Sức mạnh của Amazon Bedrock
- Phần demo về **Bedrock Agents** và **RAG** cho thấy tiềm năng to lớn trong việc xây dựng các ứng dụng thông minh có khả năng suy luận và truy xuất dữ liệu doanh nghiệp, chứ không chỉ là chat bot đơn thuần.

#### Tiếp cận Kiro IDE
- Việc tìm hiểu về **Spec-Driven Development (SDD)** trong Kiro dù được đánh giá là hơi cứng nhắc cho dự án lớn, nhưng lại mở ra hướng đi tuyệt vời cho việc prototyping nhanh chóng và giảm thiểu lỗi sai ngay từ khâu thiết kế.