---
title: "Blog 2"
date: "2025-11-05"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Triển khai thử nghiệm khôi phục để xác thực phục hồi bằng AWS Backup

*Bởi **Gabe Contreras** và **Sabith Venkitachalapathy** | 28 tháng 3 năm 2025*
*Phân loại: Advanced (300), AWS Backup, Storage, Technical*

---

Các ứng dụng quan trọng là nền tảng cho mọi thứ, từ thương mại điện tử đến chăm sóc sức khỏe, khiến cho một chiến lược sao lưu vững chắc không chỉ là một phương pháp hay nhất mà còn là một nhu cầu thiết yếu. Các mối đe dọa như **ransomware** (phần mềm tống tiền) ngày càng trở nên tinh vi hơn, và đối với người dùng AWS, chỉ có các bản sao lưu thôi là chưa đủ. Các tổ chức cần tin tưởng rằng những biện pháp bảo vệ này sẽ hoạt động hiệu quả khi thảm họa xảy ra.

Việc kiểm thử thủ công, mặc dù cần thiết, nhưng lại làm tiêu tốn tài nguyên Công nghệ thông tin. Thông qua việc kiểm thử khôi phục tự động theo lịch trình đã định, các tổ chức đạt được nhiều hơn là chỉ hiệu quả: họ thiết lập một hệ thống giúp xác minh tính toàn vẹn của bản sao lưu, hợp lý hóa việc báo cáo tuân thủ, và giải phóng nguồn nhân lực quý giá—tất cả những điều này đồng thời xây dựng sự chắc chắn rằng chiến lược bảo vệ của họ sẽ mang lại hiệu quả khi cần thiết.

Trong bài viết trước của chúng tôi, *Validating recovery readiness with AWS Backup restore testing*, chúng tôi đã tìm hiểu lý do tại sao việc kiểm thử và khôi phục của AWS Backup lại quan trọng để đáp ứng các chính sách phục hồi sau sự cố (DR) nội bộ và các yêu cầu pháp lý. Trong bối cảnh kỹ thuật số được định hình bởi các quy định như **European Union’s Digital Operational Resilience Act (DORA)** của Liên minh Châu Âu và **New York Department of Financial Services (NYDFS)**, khả năng phục hồi không chỉ là mục tiêu mà còn là một yêu cầu bắt buộc. Việc kiểm thử khôi phục của AWS Backup có thể cung cấp bằng chứng được yêu cầu bởi các quy định này; trong khi đó, các tính năng kiểm thử và kiểm toán mang lại nhiều khả năng để giúp các tổ chức xác thực và báo cáo về những nỗ lực tăng cường khả năng phục hồi của mình.

Trong bài viết này, bạn sẽ tìm hiểu cách để thiết lập cấu hình **AWS Backup restore testing** và một số phương pháp hay nhất cần cân nhắc khi tạo kế hoạch của riêng mình. Bạn cũng sẽ nhận được một ví dụ để xem cách kiểm tra khôi phục đầu cuối hoạt động trong thực tế.

## Cơ chế hoạt động của kiểm thử khôi phục AWS Backup

**AWS Backup restore testing** cho phép người dùng kiểm thử việc khôi phục dữ liệu theo một lịch trình định trước và xác thực dữ liệu đã được khôi phục. Khả năng thiết lập lịch trình và tạo ra các quy trình tự động để kiểm tra việc khôi phục dữ liệu giúp giảm bớt công sức thủ công và giúp đáp ứng các yêu cầu về tuân thủ.

Nếu không có kiểm thử khôi phục tự động, nhân sự có thể sẽ phải chọn các hệ thống và các điểm khôi phục, hoàn thành việc khôi phục thủ công, và yêu cầu các đội ngũ ứng dụng xác thực dữ liệu đã được khôi phục. Việc này tiêu tốn thời gian và tài nguyên của nhiều đội ngũ, mà lẽ ra có thể được sử dụng hiệu quả hơn để cải thiện các ứng dụng. Khi tự động hóa quy trình này, chúng ta có thể tạo ra các quy trình xác thực dữ liệu để xác thực các lần khôi phục một cách thường xuyên.

Một **AWS Backup restore testing plan** được xây dựng qua hai giai đoạn:

1. Đầu tiên, bạn tạo kế hoạch kiểm thử khôi phục.
2. Thứ hai, bạn tạo các lựa chọn tài nguyên được bảo vệ sẽ được khôi phục bởi kế hoạch kiểm thử đó.

Khi AWS Backup hoàn tất việc khôi phục, bạn có thể xây dựng quy trình xác thực cho việc kiểm thử khôi phục bằng các hàm **AWS Lambda** được kích hoạt bởi **Amazon EventBridge**. Các hàm Lambda có thể thực hiện nhiều hoạt động xác thực khác nhau—bao gồm kiểm tra khả năng kết nối, truy xuất các đối tượng từ Amazon S3, hoặc lấy trạng thái của khóa mã hóa để xác thực dữ liệu thực tế—sau đó báo cáo lại cho AWS Backup về việc liệu quá trình xác thực đó thành công hay thất bại. Sau khi kiểm thử khôi phục hoàn tất, bạn có thể sử dụng các báo cáo của **AWS Backup Audit Manager** để chứng minh sự tuân thủ khi cần thiết.

## Xây dựng kế hoạch khôi phục thử nghiệm

Bước đầu tiên của việc triển khai kiểm thử khôi phục là xây dựng kế hoạch kiểm thử khôi phục. Vì có nhiều yếu tố cần cân nhắc về tần suất kiểm thử và những gì cần kiểm thử, chúng tôi sẽ tập trung vào các phương pháp tốt nhất và các khuyến nghị. Một kế hoạch kiểm thử khôi phục bao gồm ba phần:

* **Tần suất kiểm tra (Test frequency)**
* **Quy định thời gian bắt đầu (Start within time)**
* **Các tiêu chí để lựa chọn điểm phục hồi (Recovery point selection criteria)**

Với *Tần suất kiểm thử*, bạn nên kiểm thử các tài nguyên quan trọng hàng ngày hoặc hàng tuần. Bạn nên kiểm thử các tài nguyên trong khoảng thời gian lưu giữ của chúng, nghĩa là nếu chúng ta giữ các điểm khôi phục trong 14 ngày, bạn nên cho việc kiểm thử chạy thường xuyên hơn thế.

*Quy định thời gian bắt đầu* phụ thuộc vào số lượng điểm khôi phục bạn sẽ kiểm thử và mỗi lần khôi phục mất bao lâu để hoàn thành. Mỗi dịch vụ có **maximum concurrent restores** (giới hạn phục hồi đồng thời tối đa) được phép, và bạn cần đảm bảo các kế hoạch của mình được giãn cách ra để không vượt quá giới hạn đó.
![Hình 1: Ví dụ về cấu hình của kế hoạch kiểm thử](/images/2-Proposal/Blog2_1.png)

Việc Lựa chọn điểm khôi phục có thể bao gồm tất cả hoặc các kho lưu trữ cụ thể, khung thời gian cho các điểm khôi phục đủ điều kiện, và liệu có bao gồm các loại tài nguyên khôi phục tại một thời điểm cụ thể (PITR) hay không. Bạn có thể có một kho lưu trữ cho mỗi tài khoản, hoặc nhiều kho lưu trữ dựa trên các loại ứng dụng hoặc các cấp độ (tiers). Nếu bạn đang sao chép dữ liệu đến một tài khoản sao lưu trung tâm, một thiết kế tối ưu sẽ là tạo ra một logically air-gapped (LAG) vault cho mỗi tài khoản nguồn.
![Hình 2: Mẫu tiêu chí lựa chọn điểm khôi phục AWS Backup](/images/2-Proposal/Blog2_2.png)

Sau khi tạo kế hoạch kiểm thử khôi phục, bạn chuyển sang giai đoạn 2: tạo các lựa chọn tài nguyên được bảo vệ. Với mỗi lựa chọn tài nguyên, bạn phải chọn một loại tài nguyên duy nhất, chẳng hạn như Amazon S3 hoặc **Amazon Relational Database Service (Amazon RDS)**. Sau khi chọn một loại tài nguyên, kiểm thử khôi phục cho phép bạn tùy chỉnh thêm những tài nguyên cụ thể nào sẽ được chọn. Mỗi kế hoạch kiểm thử khôi phục cho phép tối đa 30 lựa chọn tài nguyên được bảo vệ. Khi tạo phần gán tài nguyên, bạn có thể chọn vai trò IAM mặc định của AWS Backup. Nếu vai trò mặc định không tồn tại, vai trò đó sẽ được tạo ra với các quyền phù hợp.
![Hình 3: Mẫu phân bổ tài nguyên cho S3](/images/2-Proposal/Blog2_3.png)

Bạn có thể lọc các tài nguyên theo lựa chọn riêng lẻ hoặc theo thẻ, điều này cho phép bạn chọn các tài nguyên cụ thể để kiểm thử dựa trên yêu cầu của mình. Trong Hình 4, chúng tôi chọn S3 làm loại tài nguyên, sau đó chọn lọc theo thẻ (tag) để chọn các bucket cụ thể.

Mỗi dịch vụ có một bộ siêu dữ liệu khôi phục khả thi riêng, cung cấp các giá trị mặc định để thực hiện kiểm thử khôi phục thành công, trong đó AWS Backup sẽ tự suy ra một bộ siêu dữ liệu khôi phục ở mức tối thiểu. Ngoài ra còn có siêu dữ liệu có thể ghi đè mà bạn có thể thay đổi để ghi đè lên các giá trị mặc định. Bạn có thể đọc thêm về siêu dữ liệu được suy ra và siêu dữ liệu có thể ghi đè trong documentation của AWS Backup.
![Hình 4: Ví dụ về lựa chọn tài nguyên cần bảo vệ được lọc bằng thẻ](/images/2-Proposal/Blog2_4.png)

Sau khi xác định kế hoạch tổng thể và lựa chọn nguồn lực, chúng ta có một kế hoạch hoạt động đầy đủ như trong Hình 5.
![Hình 5: Ví dụ về cấu hình hoàn chỉnh của AWS Backup restore testing plan](/images/2-Proposal/Blog2_5.png)

## Triển khai xác thực khôi phục
Việc cấu hình một kế hoạch kiểm thử khôi phục mới chỉ là một nửa công việc; bạn còn phải xác minh rằng dữ liệu đã được khôi phục của mình có thể sử dụng được. AWS Backup gửi các sự kiện của Amazon EventBridge cho những thay đổi về trạng thái của công việc khôi phục. Chúng ta có thể sử dụng các sự kiện này để kích hoạt một hàm AWS Lambda khi một công việc kiểm thử khôi phục chuyển sang trạng thái hoàn thành, điều này cho phép các đội ngũ ứng dụng tạo ra mã code để kiểm thử dữ liệu của họ. Mã kiểm thử phụ thuộc vào dịch vụ bạn đang bảo vệ, nhưng có thể bao gồm việc truy xuất các đối tượng từ một bucket S3 hoặc truy vấn  Amazon DyanamoDB. Sau khi hàm Lambda của bạn chạy xong, nó có thể báo cáo lại cho AWS Backup về việc thành công hay thất bại. Hình 6 minh họa quy trình xác thực khôi phục mẫu này.
![Hình 6: Quy trình kiểm tra xác thực khôi phục AWS Backup](/images/2-Proposal/Blog2_6.png)

Nếu bạn có nhiều kế hoạch kiểm tra khôi phục, bạn có thể tùy chỉnh quy tắc EventBridge để gửi các sự kiện nhất định đến các chức năng tương ứng (như minh họa trong Hình 7) bằng cách bao gồm  Amazon Resource Name (ARN) của kế hoạch kiểm tra khôi phục. Việc sử dụng ARN của kế hoạch kiểm tra khôi phục cũng cho phép bạn lọc bỏ các lần khôi phục thủ công.
![Hình 7: Ví dụ về khuôn mẫu sự kiện EventBridge cho công việc khôi phục](/images/2-Proposal/Blog2_7.png)

Sau khi tạo mẫu sự kiện , bạn chọn một mục tiêu để gửi sự kiện đến. Nếu bạn có nhiều loại tài nguyên yêu cầu các tiêu chí kiểm thử riêng biệt, một hàm Lambda điều phối có thể giúp gửi các sự kiện cho các loại tài nguyên khác nhau đến đúng quy trình xác thực. Hàm Lambda điều phối này sẽ kiểm tra loại tài nguyên và định tuyến sự kiện đến hàm Lambda xác thực dữ liệu tương ứng. Nếu bạn có nhiều loại tài nguyên được bảo vệ, bạn sẽ chọn hàm Lambda điều phối này làm mục tiêu cho các sự kiện EventBridge, như được thấy trong Hình 8.
![Hình 8: Mẫu lựa chọn mục tiêu cho quy tắc EventBridge](/images/2-Proposal/Blog2_8.png)

Sau đây là mẫu mã điều phối Lambda khôi phục:

```python
import json
import boto3
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)
lambda_client = boto3.client('lambda')

def handler(event, context):
    logger.info("Handling event: %s", json.dumps(event))
    resource_type = event.get('detail', {}).get('resourceType', '')
    function_name = None

    try:
        if resource_type == "RDS":
            function_name = "RDSRestoreValidation"
            logger.info("Resource is an RDS instance. Invoking Lambda function: %s", function_name)
        elif resource_type == "S3":
            function_name = "S3RestoreValidation"
            logger.info("Resource is an S3 bucket. Invoking Lambda function: %s", function_name)
        else:
            raise ValueError(f"Unsupported resource type: {resource_type}")

        # Invoke the appropriate Lambda function
        response = lambda_client.invoke(
            FunctionName=function_name,
            Payload=json.dumps(event),
            InvocationType="RequestResponse"
        )
        logger.info("Lambda invoke response: %s", response)

    except Exception as e:
        logger.error("Error during Lambda invocation: %s", str(e))
        raise e

    logger.info("Finished processing event for resource type: %s", resource_type)
```

Trong đoạn mã của hàm Lambda điều phối khôi phục, bạn có thể thấy rằng nếu loại tài nguyên khớp với S3, nó sẽ chuyển tiếp toàn bộ sự kiện đến một hàm Lambda khác có tên là S3RestoreValidation. Hàm S3RestoreValidation này sau đó sẽ tiến hành xác thực việc khôi phục trên một tài nguyên S3 và báo cáo lại cho AWS Backup về việc xác thực đó thành công hay thất bại.

```python
import json
import boto3
import logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)
s3_client = boto3.client('s3')
backup_client = boto3.client('backup')
def handler(event, context):
    logger.info("Handling event: %s", json.dumps(event))
    restore_job_id = event.get('detail', {}).get('restoreJobId', '')
    resource_type = event.get('detail', {}).get('resourceType', '')
    created_resource_arn = event.get('detail', {}).get('createdResourceArn', '')
    validation_status = "SUCCESSFUL"
    validation_status_message = "Restore validation completed successfully"
    try:
        if resource_type == "S3":
            bucket_name = get_bucket_name_from_arn(created_resource_arn)
            # List objects in the bucket
            response = s3_client.list_objects_v2(Bucket=bucket_name)
            # Check if the bucket contains more than 1 object
            object_count = response.get('KeyCount', 0)
            if object_count > 1:
                logger.info(f"Bucket {bucket_name} contains more than 1 object. Validation successful.")
            else:
                logger.info(f"Bucket {bucket_name} contains 1 or fewer objects. Validation failed.")
                validation_status = "FAILED"
                validation_status_message = f"Bucket {bucket_name} contains only       {object_count} object(s)."
        else:
            validation_status = "FAILED"
            validation_status_message = f"Unsupported resource type: {resource_type}"
        # Report validation result to AWS Backup
        backup_client.put_restore_validation_result(
            RestoreJobId=restore_job_id,
            ValidationStatus=validation_status,
            ValidationStatusMessage=validation_status_message
        )
        logger.info("Restore validation result sent successfully")
    except Exception as e:
        logger.error("Error during restore validation: %s", str(e))
        validation_status = "FAILED"
        validation_status_message = f"Restore validation encountered an error: {str(e)}"
        # Report failure result to AWS Backup
        backup_client.put_restore_validation_result(
            RestoreJobId=restore_job_id,
            ValidationStatus=validation_status,
            ValidationStatusMessage=validation_status_message
        )
    logger.info("Finished processing restore validation for job ID: %s", restore_job_id)
def get_bucket_name_from_arn(arn):
    arn_parts = arn.split(":")
    resource_parts = arn_parts[-1].split("/")
    return resource_parts[-1]
```

Đoạn mã S3RestoreValidation xác thực khôi phục S3 bằng cách xác minh rằng bucket đó có nhiều hơn một đối tượng bên trong. Sau khi kiểm tra, nó sẽ báo cáo lại cho AWS Backup về việc liệu lần khôi phục đó đã hoàn thành thành công hay chưa. Một lần khôi phục và xác thực hoàn toàn thành công sẽ cho ra một bản tóm tắt như Hình 9. Trạng thái của công việc sẽ là Completed (Hoàn thành), và trạng thái xác thực sẽ là Successful (Thành công). Khi thiết lập trạng thái xác thực trong mã Lambda của bạn, bạn có thể tùy chọn thêm một thông báo xác thực, thông báo này sẽ xuất hiện trong giao diện điều khiển và các API của AWS Backup. Bạn có thể đọc thêm về xác thực khôi phục và các ví dụ trong tài liệu hướng dẫn.
![Hình 9: Ví dụ về việc hoàn tất thử nghiệm khôi phục AWS Backup](/images/2-Proposal/Blog2_9.png)

AWS Backup tự động bắt đầu quá trình xóa tài nguyên đã được khôi phục khi việc xác thực được gửi đi hoặc khi khoảng thời gian dọn dẹp hết hạn. Thời gian xóa có thể khác nhau tùy thuộc vào loại tài nguyên. Hầu hết các tài nguyên được xóa nhanh chóng, nhưng một số có thể mất nhiều thời gian hơn. Ví dụ, việc xóa một bucket S3 là một quy trình gồm hai bước: đầu tiên là thêm các quy tắc vòng đời để xóa các đối tượng, và sau đó là xóa bucket khi nó đã trống. Các quy tắc vòng đời này có thể mất vài ngày để được thực thi.

## Những cân nhắc khi kiểm tra khôi phục AWS Backup

Bây giờ bạn đã hiểu các phương pháp hay nhất để tạo kế hoạch kiểm tra và xác thực khôi phục, vẫn còn một số chi tiết triển khai khác mà bạn nên cân nhắc.

## Tối ưu chi phí

Tối ưu hóa chi phí đóng vai trò quan trọng trong suốt vòng đời sao lưu, bao gồm cả thử nghiệm khôi phục. Sau đây là cách quản lý chi phí hiệu quả:

* **Chọn tài nguyên một cách hợp lý:** Sử dụng thẻ hoặc lựa chọn để chỉ kiểm tra các tài nguyên quan trọng, tránh các tài nguyên không phải tài nguyên sản xuất trừ khi việc tuân thủ yêu cầu.
* **Lên lịch kiểm tra theo mức độ quan trọng:** Lên lịch kiểm tra theo mức độ quan trọng (hàng ngày hoặc hàng tuần đối với các tài nguyên quan trọng, hàng quý hoặc nửa năm đối với các tài nguyên khác) phù hợp với chính sách và thời gian lưu giữ (ví dụ: kiểm tra trong vòng 14 ngày nếu thời gian lưu giữ là 14 ngày).
* **Tối ưu hóa thời gian lưu giữ:** Giảm thiểu thời gian khôi phục dữ liệu để giảm chi phí. Thiết lập thời gian xóa dựa trên các thử nghiệm tự động.

Giá tham khảo cho thử nghiệm khôi phục có thể được tìm thấy trên trang giá **AWS Backup pricing page**.

## Kiểm toán và báo cáo sao lưu AWS

**AWS Backup Audit Manager** giúp bạn đảm bảo các chính sách và tài nguyên sao lưu của mình tuân thủ các tiêu chuẩn nội bộ hoặc quy định. Công cụ này theo dõi liệu các tài nguyên có được sao lưu hay không, tần suất sao lưu, liệu các kho lưu trữ có được cách ly về mặt logic hay không, và liệu thời gian khôi phục có đáp ứng mục tiêu hay không. Các khuôn khổ kiểm toán của AWS Backup Audit Manager cho phép thực hiện điều này bằng cách cung cấp các cơ chế kiểm soát có sẵn hoặc các tùy chọn tùy chỉnh để đảm bảo tài nguyên tuân thủ chính sách.

Các báo cáo kiểm toán cung cấp bằng chứng về sự tuân thủ để có thể chia sẻ. Có hai loại báo cáo:
1. **Báo cáo công việc:** Hiển thị các công việc đã hoàn thành và đang hoạt động trong 24 giờ qua (ví dụ: báo cáo công việc khôi phục cho các lần khôi phục gần đây).
2. **Báo cáo tuân thủ:** Giám sát trạng thái tài nguyên hoặc các cơ chế kiểm soát của khuôn khổ.

Các tài khoản quản lý có được khả năng hiển thị trên nhiều tài khoản để tạo các báo cáo trên toàn tổ chức. Hãy xem tài liệu của **AWS Backup documentation** để biết các bước tạo báo cáo và thông tin chi tiết về cách sử dụng các khuôn khổ.

## Khôi phục kế hoạch thử nghiệm

Khi tạo các kế hoạch kiểm thử khôi phục, hãy đảm bảo rằng các kế hoạch của bạn đáp ứng các yêu cầu kiểm thử và hoàn thành đúng hạn. Mỗi loại tài nguyên có một giới hạn về số lượng công việc khôi phục đồng thời từ các kế hoạch kiểm thử (không phải các lần khôi phục theo yêu cầu).

Khung thời gian **"Start within"** từ giai đoạn 1 là yếu tố then chốt. Cấu hình "Start within" có nghĩa là tất cả các lựa chọn tài nguyên cho một kế hoạch kiểm thử khôi phục phải bắt đầu trong khung thời gian này, và bạn cần cẩn thận để không vượt quá giới hạn đồng thời.

*Ví dụ: Amazon S3 cho phép 30 lần khôi phục đồng thời, vì vậy việc chọn 90 bucket với khung thời gian một giờ có nguy cơ gây ra sự chậm trễ.*

Để lên kế hoạch hiệu quả, hãy sử dụng một khung thời gian "start within" dài hơn hoặc tạo nhiều kế hoạch với các thời điểm bắt đầu so le nhau—đặc biệt là khi các bài kiểm thử thường xuyên (hàng ngày/hàng tuần) và định kỳ (hàng tháng/hàng quý) chạy cùng lúc. Hãy kiểm tra các giới hạn có thể điều chỉnh trong **documentation** và yêu cầu tăng giới hạn nếu cần.

## Trực quan hoá hoàn chỉnh quá trình thử nghiệm

Để xem cách thức hoạt động của kiểm thử khôi phục toàn diện và cách triển khai và tích hợp các kế hoạch kiểm thử, chúng tôi đã đưa vào một kế hoạch kiểm thử khôi phục mẫu. Kế hoạch mẫu này giúp bạn hình dung từng bước của quy trình và xem cách khôi phục và xác thực tương tác với nhau.

Đây là một **AWS CloudFormation** được cấu hình sẵn, chạy tự động theo lịch trình hàng ngày.

### Điều kiện tiên quyết

Các điều kiện tiên quyết sau đây là cần thiết để hoàn thành giải pháp này:
* AWS Backup được cấu hình trong tài khoản của bạn.
* Điểm khôi phục của Amazon S3 và/hoặc Amazon RDS.

### Khởi chạy AWS CloudFormation stack

Mẫu AWS CloudFormation này triển khai mọi thứ cần thiết để tự động kiểm tra khôi phục cả Amazon S3 và Amazon RDS.

[https://awsstorageblogresources.s3.us-west-2.amazonaws.com/blog1418/CFAWSBackupRestoreTestingV15.yaml](https://awsstorageblogresources.s3.us-west-2.amazonaws.com/blog1418/CFAWSBackupRestoreTestingV15.yaml)

### Chạy kế hoạch thử nghiệm khôi phục

Sau khi triển khai, không cần can thiệp thủ công để chạy kế hoạch. Kế hoạch kiểm tra khôi phục chạy một lần mỗi ngày trên tất cả các tài nguyên được kế hoạch đó chọn. Như đã lưu ý trong **Hình 6**, AWS Backup hoàn tất quá trình khôi phục, sau đó chạy các hàm Lambda để xác thực các lần khôi phục.

Khi quá trình xác thực hoàn tất thành công, quá trình xác thực sẽ hiển thị như trong **Hình 10**.
![Hình 10: Ví dụ về việc hoàn tất kiểm tra khôi phục AWS Backup](/images/2-Proposal/Blog2_10.png)

## Dọn dẹp

Tất cả các tài nguyên đã được khôi phục từ kế hoạch kiểm thử khôi phục sẽ tự động bị xóa sau bốn giờ. Nếu bạn đang sử dụng kiểm thử khôi phục cho các tài nguyên Amazon S3, thì việc xóa các bucket S3 chứa dữ liệu sẽ mất nhiều thời gian hơn. Điều này là do các quy tắc vòng đời (lifecycle rules) mất vài ngày để thực thi. Để tránh phát sinh thêm chi phí, hãy xóa ngăn xếp (stack) CloudFormation, thao tác này sẽ xóa các kế hoạch kiểm thử khôi phục và dừng các lần kiểm thử sau này. Để biết hướng dẫn, hãy tham khảo mục Deleting a stack on the CloudFormation console.

## Tổng kết

Kiểm thử khôi phục của AWS Backup là một tính năng linh hoạt và có thể mở rộng, cho phép bạn điều chỉnh một giải pháp phù hợp với nhu cầu của tổ chức mình. Bạn có thể bắt đầu bằng cách tìm hiểu các chính sách của tổ chức, sau đó khám phá các khả năng kiểm thử khôi phục của AWS Backup trong AWS Management Console và học cách tích hợp kiểm thử tự động vào các chiến lược phục hồi sau thảm họa (DR) và khả năng phục hồi không gian mạng của mình. Để triển khai kiểm thử khôi phục của AWS Backup trong môi trường của bạn, hãy truy cập AWS Backup documentation. Bạn cũng có thể làm việc với các Kiến trúc sư Giải pháp của AWS để thiết kế một chiến lược xác thực sao lưu toàn diện được điều chỉnh cho phù hợp với nhu cầu của tổ chức bạn.

Thông điệp rất rõ ràng: việc xác thực sao lưu tự động không còn là một lựa chọn tùy chọn, đó là một yêu cầu cơ bản để đảm bảo hoạt động kinh doanh liên tục trong thời hiện đại. Việc kiểm thử thường xuyên giúp đáp ứng các chính sách nội bộ, các yêu cầu pháp lý, và các yêu cầu về khả năng phục hồi không gian mạng, trong khi đó, kiểm thử khôi phục của AWS Backup cung cấp một giải pháp hiệu quả, có khả năng mở rộng để đảm bảo sự sẵn sàng phục hồi.

## Thông tin tác giả

### Gabe Contreras
Gabe Contreras là Kiến trúc sư Giải pháp Lưu trữ Cao cấp cho bộ phận Tài khoản Chiến lược. Anh luôn mong muốn được trao đổi sâu với khách hàng để tìm ra giải pháp tốt nhất cho nhu cầu của họ. Anh luôn thích tìm hiểu cách thức vận hành của mọi thứ và giải quyết các vấn đề phức tạp.

### Sabith Venkitachalapathy
Sabith Venkitachalapathy là chuyên gia thiết kế các giải pháp phục hồi AWS, đảm bảo khả năng phục hồi sau thảm họa và tính khả dụng cao cho các khối lượng công việc quan trọng. Tập trung vào Dịch vụ Tài chính (FSI) và Chăm sóc Sức khỏe và Khoa học Đời sống (HCLS), Sabith tận dụng AWS để giải quyết các thách thức trong ngành và thúc đẩy đổi mới. Anh chia sẻ những hiểu biết thực tế để giúp các tổ chức xây dựng kiến trúc đám mây an toàn và linh hoạt.
