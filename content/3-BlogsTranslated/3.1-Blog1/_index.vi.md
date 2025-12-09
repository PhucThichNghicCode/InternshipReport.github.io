---
title: "Blog 1"
date: "2025-11-05"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Volkswagen và AWS đã xây dựng một quy trình MLOps hoàn chỉnh cho Nền Tảng Kỹ Thuật Số như thế nào

**Bởi:** Gabriel Zylka, Chandana Keswarkar, and Sandro Zangiacomi
**Ngày:** 03 tháng 03 năm 2025

> **Từ khóa:** Amazon API Gateway, Amazon DataZone, Amazon EventBridge, Amazon SageMaker, Amazon Simple Storage Service (S3), Automotive, AWS CodeArtifact, AWS CodeCommit, AWS Lake Formation, AWS Service Catalog, AWS Step Functions, AWS Well-Architected Framework, Industries

## Bối cảnh

Vào năm 2019, Volkswagen AG (VW) và Amazon Web Services (AWS) đã hình thành 1 chiến lược hợp tác để phát triển Nền Tảng Kỹ Thuật Số (DPP), chiến lược này được thiết kế để nâng cao hiệu quả sản xuất và vận chuyển của VW lên 30% đồng thời giảm chi phí sản xuất ở mức tương tự. DPP giúp truy cập dữ liệu từ các thiết bị và hệ thống sản xuất của VW, giúp việc xây dựng và triển khai các ứng dụng mới đơn giản và nhanh hơn từ 2-3 lần, đồng thời giảm chi phí thử nghiệm tạo điều kiện chia sẻ các giải pháp giữa các doanh nghiệp VW. Một giải pháp chính được áp dụng là quy trình MLOps hoàn chỉnh cho các bài toán liên quan tới Học máy (ML). Bài viết này trình bày về kiến trúc đã được sử dụng để chuẩn hoá toàn bộ vòng đời của 1 dự án ML, các kinh nghiệm hay nhất, và làm thế nào để triển khai một giải pháp MLOps tương tự trong tổ chức của bạn.

Đội ngũ VW đã triển khai hơn 100 trường hợp sử dụng tại các công ty và thương hiệu của VW. Phần lớn các trường hợp này là giải pháp dự trên ML trong các lĩnh vực như dự đoán bảo trì, chất lượng, và quá trình tối ưu. Ví dụ, dự đoán bảo trì cho robot súng hàn liên quan đến các cảm biến phát hiện lỗi động cơ hoặc mạch hàn. Những mô hình ML được yêu cầu để dự đoán sớm các lỗi này sớm từ hàng ngàn con robot tại nhiều nhà máy khác nhau. Nhiều nhóm khoa học dữ liệu đã làm việc để tạo ra các giải pháp ML sẵn sàng đưa vào vận hành, tuân thủ các tiêu chuẩn bảo mật của tập đoàn. Tuy nhiên, cách làm phân tán này đã bốc bộ ra nhiều vấn đề:

* **Cách phát triển k nhất quán:** Mỗi nhà máy tự phát triển giải pháp độc lập, đẫn đến kết quả vận hành bị phân mảnh và không có một chiến lược thống nhất. Điều này tạo ra 1 tập hợp các giải pháp thiếu liên kết và rời rạc.
* **Phát triển thiếu hiệu quả:** Những nỗ lực dư thừa phát sinh từ các nhóm VW khi phải tạo lại những các thành phần cơ sở hạ tầng tương tự nhau, mỗi thành phần lại yêu cầu đánh giá bảo mật khác nhau làm tăng sự phức tạp.
* **Ảnh hưởng đến thời gian và tài nguyên:** Các triển khai ban đầu yêu cầu 2 nhân viên full-time làm việc 2 tháng cho mỗi luồng việc. Việc đào tạo thành viên mới và hoàn thành đánh giá bảo mật cũng lâu hơn vì mỗi nơi có cách triển khai riêng.
* **Vấn đề quản lý quy trình:** Với việc không có quy trình tiêu chuẩn, các nhóm VW đã gặp khó khăn trong việc quản lý, truy vết và quản lý phiên bản của mô hình. Việc này làm ảnh hưởng tới tính minh bạch.
* **Thách thức trong việc bảo trì và chất lượng:** Các triển khai đa dạng dẫn đến chất lượng không đồng đều, lãng phí tài nguyên, ảnh hưởng tiêu chuẩn kiểm thử, gây khó khăn trong việc áp dụng các phương pháp tốt hơn.

Những vấn đề trên đã tạo ra những ảnh hưởng tài chính, làm chậm thời gian ra mắt sản phẩm, tăng chi phí bảo trì và tạo thêm rủi ro trong việc bảo mật. Việc chia sẻ kiến thức trở nên khó khăn, dẫn đến nỗ lực phí sức, lãng phí tài nguyên vào những việc không tạo ra sự khác biệt và tăng thêm gánh nặng cho việc vận hành và bảo trì. Để giải quyết những vấn đề này, VW đã hợp tác với AWS Professional Services để xây dựng 1 giải pháp MLOps an toàn hơn, có khả năng mở rộng cho các doanh nghiệp sử dụng ML để triển khai trên DPP.

## Kiến trúc MLOps

Kiến trúc được triển khai tại VW đã cho thấy cách MLOps tự động hóa mọi công đoạn, tạo ra một quy trình hiệu quả để quản lý toàn bộ vòng đời của mô hình học máy. Để tìm hiểu sâu hơn, bạn có thể xem bài viết *MLOps foundation roadmap for enterprises with Amazon SageMaker*.
![Kiến trúc đa tài khoản của MLOps](/images/2-Proposal/Blog1.png)

Một chiến lược đa tài khoản giúp quản lý nhiều mô hình. Dưới đây là cách mỗi tài khoản hoạt động:

* **Tài khoản Data (Dữ liệu):** Tài khoản này đóng vai trò như một trung tâm cho việc quản lý dữ liệu, giám sát tất cả quá trình đưa dữ liệu từ các nguồn như hệ thống on-premises hoặc đưa những môi trường khác lên đám mây. Quản trị viên điều khiển và hạn chế truy cập vào các cột dữ liệu cụ thể để đáp ứng yêu cầu của trường hợp sử dụng, đảm bảo tuân thủ thông qua việc ẩn danh khi cần thiết. Để tìm hiểu thêm về cách VW quản lý quyền truy cập và bảo mật dữ liệu sử dụng Amazon DataZone, hãy tham khảo blog post.
* **Tài khoản EXP (Thử nghiệm):** Tài khoản này cung cấp một môi trường chuyên dụng cho nhóm khoa học dữ liệu của VW để thực hiện khai phá dữ liệu, thử nghiệm và huấn luyện mô hình. Tài khoản EXP triển khai tất cả tài nguyên trong một VPC được cô lập mà không có quyền truy cập internet. Để có thể sử dụng thư viện của bên thứ ba, một kho lưu trữ AWS CodeArtifact cung cấp quyền truy cập an toàn vào các kho lưu trữ công cộng như PyPI. Các Data Scientist cam kết thay đổi code vào kho lưu trữ  AWS CodeCommit (hoặc những nhà cung cấp khác như GitLab, vì quyền truy cập AWS CodeCommit cho người dùng mới đã kết thúc). Khi huấn luyện hoặc suy luận yêu cầu chứa hình ảnh tuỳ chỉnh, các Data Scientists cam kết code được chuyển đến một kho lưu trữ. Sau đó, một CI/CD sẽ quét, kiểm tra và xây dựng hình ảnh trước khi đưa chúng tới một trung tâm Amazon ECR đăng ký ở tài khoản RES.
* **Tài khoản RES (Tài nguyên):** Tài khoản này quản lý tất cả cơ sở hạ tầng và việc triển khai mô hình ML. Nó là nơi chứa kho lưu trữ CodeCommit cho các cơ sở hạ tầng như Code (IaC) và quy trình CI/CD của AWS CodePipeline, cho phép có thể triển khai trên tất cả các tài khoản RES, EXP, DEV, INT và PROD. Ngoài ra, tài khoản này cũng nơi nơi chứa các kho lưu trữ Amazon ECR, nơi các nhà khoa học công bố các Docker chứa các hình ảnh tùy chỉnh được sử dụng trong quá trình suy luận của mô hình. Cuối cùng, nó tạo các sản phẩm AWS Service Catalog trong tài khoản EXP để thực hiện các quy trình huấn luấn mô hình và quy trình triển khai mô hình ở tài khoản RES.
* **Tài khoản DEV (Phát triển):** Tài khoản này đóng vai trò là môi trường phát triển nơi các nhóm VW ban đầu triển khai các mô hình ML tới các điểm kết thúc của  Amazon SageMaker. Tại đây, các mô hình sẽ trải qua quá trình kiểm thử hoàn chỉnh bởi VW cho tất cả những chỉ số của mô hình như hiệu suất và các thông số hạ tầng như thời gian phản hồi và khả năng hoạt động. Trong tài khoản DEV, quản trị viên thường cấp quyền truy cập thủ công cho các nhân viên khoa học dữ liệu và nhóm DevOps để kiểm tra và xử lý sự cố của việc triển khai. Sau khi kiểm tra hoàn tất, bước phê duyệt thủ công trong quy trình CI/CD tại tài khoản RES sẽ đẩu việc triển khai sang giai đoạn INT.
* **Tài khoản INT (Tích hợp):** Tài khoản này hoạt động như 1 môi trường staging để triển khai mô hình ML nhằm xác thực việc triển khai và tích hợp cơ sở hạ tầng đã thành công trước khi tiến hành triển khai vào môi trường PROD. Không giống như môi trường DEV, việc triển khai trong tài khoản INT có thể chỉ được truy cập thông qua các quyền chỉ đọc. Sau khi vượt qua tất cả các quy trình kiểm tra, nhóm DevOps sẽ phê duyệt thông qua quy trình CI/CD trong tài khoản RES để triển khai mô hình thành sản phẩm.
* **Tài khoảng PROD (Sản xuất):** Tài khoản này lưu trữ phiên bản của mô hình ML trên điểm cuối Amazon SageMaker. Trong môi trường sản xuất, bạn có thể cấu hình SageMaker endpoint with an auto scaling group để tự động mở rộng quy mô điểm cuối lên hoặc xuống dựa trên nhu cầu.

## MLOps cho Data Scientist

Machine learning lifecycle là một quá trình lặp lại, bắt đầu bằng việc xác định một vấn đề doanh nghiệp và quyết định xem ML có phải là giải pháp có thể áp dụng không. Sau khi xác nhận, quá trình sẽ bao gồm việc định hình bài toán ML, theo sau là giai đoạn dữ liệu, nơi mà các Data Engineer thu thập, khai phá, chuẩn bị và phân tích dữ liệu thông qua trực quan hoá dữ liệu. Tiếp theo là Feature Engineering, nơi những kĩ thuật cụ thể như mã hoá, chuẩn hoá và xử lý dữ liệu bị mất. Sau đó là giải đoạn phát triển mô hình, giai đoạn này sẽ bao gồm việc lựa chọn một thuật toàn phù hợp, huấn luyện mô hình, tinh chỉnh các siêu tham số và đánh giá hiệu suất bằng các chỉ số chỉ số đã được định nghĩa trước. Một khi mô hình đạt được tiêu chí hiệu suất mong muốn, nó sẽ được triển khai ra môi trường sản xuất. Theo thời gian, hiệu suất mô hình có thể bị giảm, phải liên tục theo dõi, gỡ lỗi, huấn luyện lại và tái triển khai để duy trì hiệu quả mô hình. Các bước sau đây mô tả luồng làm việc của một Data Scientist cho những giải pháp MLOPS trên các tài khoản khác nhau:

1.  **Thu thập dữ liệu và Chuẩn bị dữ liệu:** Data Engineer tạo qua các quy trình trích xuất, chuyển đổi và tải (ETL) kết hợp với nhiều nguồn dữ liệu và chuẩn bị các bộ dữ liệu cần thiết cho các bài toàn ML trong tài khoản DATA. Dữ liệu được lập danh mục bằng  AWS Glue Data Catalog và chia sẻ với những người dùng và tài khoản khác thông qua  AWS Lake Formation để quản lý. Data Scientist được cấp quyền truy cập an toàn vào những bộ dataset cụ thể từ tài khoản DATA.
2.  **Khai phá dữ liệu và Phát triển mô hình:** Mỗi Data Scientist nhận được một hồ sơ người dùng  Amazon SageMaker Studio với một vai trò IAM và Nhóm Bảo Mật, để truy cập vào SageMaker  Studio và bộ datasets cụ thể của họ trong Amazon S3. Trong không gian làm việc cá nhân của mình, Data Scientist thực hiện các tác vụ như khai phá dữ liệu, huấn luyện mô hình, điều chỉnh siêu tham số, xử lý dữ liệu và đánh giá mô hình, sử dụng Jupyter Notebooks hoặc những dịch vụ SageMaker. Điều này có thể được mở rộng với Amazon SageMaker Feature Store để tái sử dụng. Để biết thêm thông tin, bạn có thể tham khảo *Enable feature reuse across accounts and teams using Amazon SageMaker Feature Store*.
3.  **Huấn luyện mô hình và Huấn luyện lại mô hình:** Sau giai đoạn thử nghiệm, Data Scientist khởi chạy “Model Building Product” từ  AWS Service Catalog. Việc này sẽ khởi tạo một stack CloudFormation để thiết lập một Sagemaker Pipeline để điều phối các tác vụ dữ liệu như xử lý, huấn luyện và đánh giá. Các mô hình ML được huấn luyện và đánh giá thành công sẽ được đăng ký vào Sagemaker Model Registry, nơi lưu trữ lịch sử phiên bản và triển khai siêu dữ liệu, chẳng hạn như các container chứa các hình ảnh và vị trí của các tệp artifact. Đối với việc huấn luyện lại sau này, Data Scientists sẽ kích hoạt Quy trình Huấn luyện, quy trình này sẽ đăng ký một phiên bản mô hình mới vào Model Registry mô hình sau khi thực thi thành công. Khi một phiên bản mô hình mới được đăng ký, một sự kiện tên là Amazon EventBridge được kích hoạt và gửi đến tài khoản RES, khởi động quy trình triển khai.
4.  **Triển khai Mô hình và Tái triển khai Mô hình:** Để tạo một quy trình triển khai mô hình, kỹ sư DevOps khởi chạy sản phẩm “Model Deployment” từ Service Catalog, tham chiếu đến mô hình đã được huấn luyện trong Model Registry. Sản phẩm này sẽ cung cấp một kho lưu trữ CodeCommit cho IaC, một CodePipeline và một quy tắc EventBridge để theo dõi phiên bản mới của mô hình từ tài khoản EXP. Quy trình CI/CD được kích hoạt bởi những thay đổi trong kho lưu trữ CodeCommit và bởi các sự kiện đến từ EventBridge. Nó sẽ truy vấn Model Registry để lấy phiên bản mới nhất và triển khai mô hình cùng các tài nguyên liên quan đến giai đoạn DEV. Sau các bước phê duyệt thủ công, mô hình sẽ tiếp tục được chuyển qua giai đoạn INT và PROD.

## Lợi ích

Quy trình MLOps mới này mang lại nhiều lợi ích quan trọng:

* **Chuẩn hoá:** Bằng cách thay thế nhiều giải pháp tuỳ chỉnh với một quy trình chung thống nhất, VW đã loại bỏ được việc phải làm đi làm lại những công việc giống nhau và thiết lập được các cách làm đồng bộ trong mọi hoạt động ML.
* **Vận hành hiệu quả:** Một kiến trúc tài khoản có cấu trúc được chia thành các môi trường khác nhau, phân chia nhiệm vụ rõ ràng và tối ưu toàn bộ vòng đời ML từ lúc thử nghiệm cho đến khi triển khai.
* **Bảo mật và Quản trị:** Các rào bảo mật được tích hợp sẵn, bao gồm các vai trò IAM chuyên dụng, các VPCs được cô lập và mã hoá giúp đảm bảo các hoạt động ML đáp ứng tiêu chuẩn bảo mật của doanh nghiệp trong khi vẫn giữ được sự linh hoạt. 
* **Khả năng mở rộng:** Giải pháp này hiện đang hỗ trợ 8 dự án tại 5 nhà máy, phục vụ cho 16 Data Scientist, với kiến trúc được thiết kế để dễ dàng mở rộng trong tương lai và đáp ứng thêm nhiều dự án mới.
* **Giảm thời gian ra mắt sản phẩm:** Một quy trình tự động và chuẩn hoá giờ đây có thể hoàn thành công việc chỉ trong vài ngày, trong khi trước đây cần tới 2 nhân viên làm full-time trong 2 tháng cho 1 dự án. Điều này giúp tăng tốc đáng kể việc triển khai mô hình.

## Kho lưu trữ mã nguồn mở

Kho lưu trữ Github cung cấp một mẫu giải pháp để triển khai hạ tầng MLOps đã được thảo luận trong bài blog này. Giải pháp này sẽ triển khai tổng cộng 13 stack AWS CDK có thể cấu hình được trên khắp 5 tài khoản AWS, cho phép bạn nhanh chóng khởi tạo nền tảng MLOps. Mẫu này rất linh hoạt và có thể được tuỳ chỉnh để đáp ứng các yêu cầu cụ thể của bạn, chẳng hạn như thêm các Service Catalog Product để tinh chỉnh các quy trình huấn luyện và triển khai mô hình. Để thực hiện việc triển khai, bạn sẽ cần quyền truy cập vào 5 tài khoản AWS cho các môi trường sau:

* EXP (Experimentation)
* RES (Resources) 
* DEV (Development)
* INT (Integration)
* PROD (Production)

Để biết các yêu cầu cần có trước và hướng dẫn triển khai, hãy tham khảo tệp `README.md` trong kho lưu trữ.

## Các Extensions khả thi

Các Extensions sau có thể nâng cao giải pháp MLOps để đáp ứng nhu cầu cho từng trường hợp cụ thể:

### Xử lý hàng loạt (Batch Processing)

Mặc dù suy luận trực tuyến cung cấp các dự đoán theo thời gian thực với độ trễ thấp, suy luận hàng loạt là lựa chọn lý tưởng cho các tình huống mà dữ liệu được đưa đến hàng loạt theo các khoảng thời gian đều đặn và không yêu cầu kết quả ngay lập tức. Suy luận hàng loạt đặc biệt phù hợp cho các nhu cầu suy luận định kỳ. Bằng cách sử dụng Amazon SageMaker, bạn có thể thực hiện suy luận hàng loạt bằng cách chạy một  batch transform job trên một Mô hình SageMaker hoặc điều phối một quy trình suy luận hàng loạt với  AWS Step Functions.. Kết quả được tự động lưu trữ trong Amazon S3.

### API Gateway

Để phục vụ các mô hình thông qua các điểm cuối API tùy chỉnh, hãy sử dụng Amazon API Gateway, một dịch vụ được quản lý hoàn toàn để tạo, xuất bản, duy trì, giám sát và bảo mật các API ở mọi quy mô. Các yêu cầu được định tuyến qua API Gateway đến AWS Lambda, dịch vụ này sẽ gọi điểm cuối của SageMaker và trả về các phản hồi cho API Gateway để kiểm thử và phục vụ các dự đoán.

### Giám sát mô hình

Amazon SageMaker Model Monitor giúp cho phép theo dõi liên tục hiệu suất của mô hình sau khi triển khai trong môi trường sản xuất. Công cụ này thu thập các mẫu dữ liệu đầu vào và các dự đoán của mô hình theo các khoảng thời gian được lên lịch, giám sát các chỉ số như chất lượng dữ liệu, chất lượng mô hình, độ lệch  và khả năng giải thích. Nếu Model Monitor phát hiện bất kỳ sự sai lệch hoặc suy giảm  nào, nó sẽ tạo ra các cảnh báo, cho phép bạn thực hiện các hành động khắc phục như thu thập dữ liệu huấn luyện mới, huấn luyện lại mô hình hoặc kiểm tra các hệ thống ở đầu nguồn . Tìm hiểu thêm về  *Monitoring in-production ML models at large scale using Amazon SageMaker Model Monitor*.

## Hàng rào an ninh

Giải pháp MLOps của VW tuân theo các phương pháp bảo mật tốt nhất của AWS từ AWS Well Architected Framework Security pillar và từ sách trắng của AWS có tên *Build a Secure Enterprise Machine Learning Platform on AWS*. Ngoài các tính năng bảo mật được triển khai trong mỗi tài khoản VW AWS, dưới đây là một số phương pháp tốt nhất khác đã được tuân thủ:

* **Bảo vệ cơ sở hạ tầng:**
    * Tất cả tài nguyên đều được phân tách trong các mạng con VPC tự quản lý.
    * Tất cả các dịch vụ AWS đều được truy cập thông qua các VPC Service Endpoints.
* **Bảo vệ dữ liệu:**
    * Mọi dữ liệu lưu trữ đều được mã hóa bằng khóa mã hóa do khách hàng quản lý.
    * Môi trường SageMaker Studio được mã hóa khi lưu trữ.
* **Xác định và quản lý quyền truy cập:**
    * Các vai trò IAM chuyên dụng được gán cho người dùng SageMaker Studio, các đường ống, công việc đào tạo và điểm cuối mô hình.
    * Các vai trò IAM được tạo bằng cách sử dụng các persona của Amazon SageMaker Role Manager để thực thi quyền truy cập có đặc quyền thấp nhất.
    * Các chính sách từ chối IAM rõ ràng để hạn chế việc đào tạo mô hình hoặc triển khai mô hình bên ngoài VPC hoặc không cần mã hóa.
* **Kiểm tra:**
    * Kiểm tra bảo mật thường xuyên được thực hiện để xác định và giảm thiểu các lỗ hổng.
    * AWS Config được sử dụng để theo dõi các thay đổi cấu hình và đảm bảo tuân thủ các chính sách bảo mật.
    * AWS Security Hub tổng hợp các phát hiện bảo mật từ nhiều dịch vụ AWS để quản lý và khắc phục tập trung. Tìm hiểu thêm về *VW secures landing zone with automated remediation of security findings*.

## Tổng kết

Sự hợp tác giữa VW và AWS đã chuyển đổi thành công một bối cảnh MLOps phân mảnh thành một quy trình sản xuất ML (Học máy) được tiêu chuẩn hóa, hiệu quả và an toàn hơn. Bằng cách triển khai một giải pháp MLOps toàn diện được xây dựng trên Amazon SageMaker, VW đã giải quyết các thách thức của việc phát triển phi tập trung, để thiết lập một vòng đời ML hợp lý, có khả năng mở rộng và an toàn hơn thông qua kiến trúc MLOps đa tài khoản. Việc triển khai này có thể đóng vai trò như một bản thiết kế cho các doanh nghiệp khác đang tìm cách tiêu chuẩn hóa các hoạt động MLOps của họ ở quy mô lớn. Nếu bạn quan tâm đến việc khám phá các giải pháp tương tự hoặc cần hướng dẫn xây dựng giải pháp MLOps của riêng mình, hãy truy cập AWS for automotive page  hoặc contact với nhóm AWS của bạn ngay hôm nay.

## Tác Giả
* **Gabriel Zylka:** Là Kỹ sư học máy tại AWS Professional Services. Anh làm việc chặt chẽ với khách hàng để đẩy nhanh hành trình chuyển đổi số sang công nghệ đám mây. Chuyên về lĩnh vực MLOps, anh tập trung vào việc sản xuất khối lượng công việc ML bằng cách tự động hóa vòng đời ML từ đầu đến cuối và giúp đạt được kết quả kinh doanh mong muốn.
* **Chandana Keswarkar:** Là Kiến trúc sư Giải pháp cao cấp AWS, chuyên hướng dẫn khách hàng trong lĩnh vực ô tô trong hành trình chuyển đổi số bằng công nghệ đám mây. Cô giúp các tổ chức phát triển và tinh chỉnh kiến trúc nền tảng và sản phẩm, đồng thời đưa ra các quyết định thiết kế sáng suốt. Trong thời gian rảnh rỗi, cô thích đi du lịch, đọc sách và tập yoga.
* **Sandro Zangiacomi:** Là chuyên gia AI dịch vụ chuyên nghiệp của AWS tại Paris. Trong vai trò hiện tại, anh hỗ trợ khách hàng điều phối quy trình làm việc máy học và xây dựng nền tảng học máy cho nhiều trường hợp sử dụng khác nhau, bao gồm cả việc triển khai GenAI. Trong thời gian rảnh rỗi, anh thích dành những khoảnh khắc chất lượng bên bạn bè tại Paris và học hỏi về hầu hết mọi thứ, từ phát triển bản thân đến thời trang.