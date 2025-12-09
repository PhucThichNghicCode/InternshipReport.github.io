---
title: "Blog 3"
date: "2025-11-05"
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# Cải thiện hiệu suất PostgreSQL bằng cách sử dụng tiện ích mở rộng pgstattuple

*bởi **Vivek Singh**, **Kiran Singh**, and **Sagar Patel** | Ngày 02 tháng 03 năm 2025 | tại Advanced (300), Amazon Aurora, Amazon RDS, PostgreSQL compatible, RDS for PostgreSQL, Technical How-to*

---

Khi các doanh nghiệp tiếp tục tạo ra và lưu trữ lượng dữ liệu khổng lồ, nhu cầu về các hệ thống quản lý cơ sở dữ liệu hiệu quả và đáng tin cậy ngày càng trở nên quan trọng. PostgreSQL, một hệ quản trị cơ sở dữ liệu quan hệ (RDBMS) mã nguồn mở, đã tự khẳng định mình là một giải pháp mạnh mẽ để xử lý các yêu cầu dữ liệu phức tạp. Một trong những thế mạnh chính của PostgreSQL nằm ở khả năng mở rộng của nó (extensibility). Thông qua một hệ sinh thái phong phú gồm các phần mở rộng và plugin, các nhà phát triển có thể nâng cao chức năng của cơ sở dữ liệu để đáp ứng các yêu cầu cụ thể. Các phần mở rộng này bao gồm từ hỗ trợ dữ liệu không gian và khả năng tìm kiếm toàn văn bản đến các kiểu dữ liệu nâng cao và các công cụ tối ưu hóa hiệu suất. Mặc dù PostgreSQL cung cấp một loạt các tính năng và khả năng, một phần mở rộng thường bị bỏ qua là **pgstattuple**—một công cụ có thể cung cấp giá trị đáng kể để có được thông tin chi tiết về hoạt động bên trong của cơ sở dữ liệu PostgreSQL.

Trong bài đăng này, chúng tôi khám phá sâu về **pgstattuple**—nó cung cấp những thông tin chi tiết nào, cách sử dụng nó để chẩn đoán các sự cố trong Amazon Aurora PostgreSQL-Compatible Edition và Amazon Relational Database Service (Amazon RDS) for PostgreSQL, và các phương pháp hay nhất để khai thác khả năng của nó.

## Tổng quan về pgstattuple

Phần mở rộng **pgstattuple** cung cấp một tập hợp các hàm để truy vấn số liệu thống kê chi tiết ở cấp độ tuple (bản ghi) trong các bảng và chỉ mục của PostgreSQL. Điều này cho phép nhìn sâu vào lớp lưu trữ vật lý mà các chế độ xem thống kê tiêu chuẩn của PostgreSQL không thể cung cấp.

Một số số liệu cấp bảng và chỉ mục mà pgstattuple cung cấp bao gồm:

* **tuple_count** – Số lượng tuple đang hoạt động (live tuples)
* **dead_tuple_count** – Số lượng tuple chết (dead tuples) chưa được dọn dẹp
* **tuple_len** – Độ dài trung bình của các tuple đang hoạt động tính bằng byte
* **free_space** – Tổng không gian trống khả dụng tính bằng byte
* **free_percent** – Tỷ lệ phần trăm không gian trống; giá trị càng cao cho thấy mức độ phình to (bloat) càng nhiều
* **dead_tuple_len** – Tổng độ dài của các dead tuple tính bằng byte
* **dead_tuple_percent** – Tỷ lệ phần trăm không gian bị chiếm dụng bởi các dead tuple

Những số liệu này không chỉ là những con số đơn thuần – chúng là một hệ thống cảnh báo sớm cho các vấn đề về sức khỏe và hiệu suất của cơ sở dữ liệu. Bằng cách theo dõi các thống kê này, bạn có thể chủ động xác định các vấn đề về lưu trữ có thể đang âm thầm ảnh hưởng đến hiệu suất cơ sở dữ liệu của mình. Dù đó là tình trạng bảng bị phình to quá mức gây tốn dung lượng đĩa, hay sự phân mảnh chỉ mục làm chậm các truy vấn, pgstattuple đều giúp phát hiện những vấn đề này trước khi chúng trở thành sự cố nghiêm trọng.

## Sử dụng pgstattuple trong Aurora và Amazon RDS

Cả Aurora và Amazon RDS đều hỗ trợ sử dụng phần mở rộng pgstattuple. Để kích hoạt nó, trước tiên bạn cần tạo phần mở rộng trong cơ sở dữ liệu của mình bằng lệnh `CREATE EXTENSION pgstattuple;`. Sau khi được kích hoạt, bạn có thể sử dụng các hàm như `pgstattuple(relation)` để nhận thông tin chi tiết về bộ nhớ vật lý được sử dụng bởi một bảng, bao gồm số lượng trang (page), tuple sống (live tuples), tuple chết (dead tuples), và nhiều hơn nữa. Hàm `pgstattuple_approx(relation)` cung cấp một ước tính nhanh hơn về các số liệu này. Bạn cũng có thể nhận các thống kê về chỉ mục bằng cách sử dụng `pgstatindex(index)`. Việc phân tích dữ liệu cấp thấp này có thể giúp xác định các bảng bị phình to (bloated) cần được vacuum, tìm các bảng có tỷ lệ tuple chết cao có thể được hưởng lợi từ việc ghi lại (rewritten), và tối ưu hóa việc sử dụng bộ nhớ vật lý của cơ sở dữ liệu của bạn.

Đầu ra của pgstattuple cung cấp những thông tin chi tiết có thể hành động để giám sát, bảo trì và tinh chỉnh hiệu suất, như sẽ được thảo luận trong các phần sau.

## Phát hiện và quản lý sự to ra của bảng

Xác định và quản lý sự phình to (bloat) là một trong những ứng dụng hữu ích nhất của pgstattuple đối với các bảng PostgreSQL. Sự phình to phát sinh khi các thao tác UPDATE và DELETE để lại không gian không sử dụng mà không được tự động thu hồi.

PostgreSQL duy trì tính nhất quán của dữ liệu thông qua mô hình Kiểm soát đồng thời đa phiên bản (Multiversion Concurrency Control - MVCC), nơi mỗi câu lệnh SQL nhìn thấy một bản ghi nhanh (snapshot) của dữ liệu từ một thời điểm trước đó, bất kể trạng thái hiện tại của dữ liệu cơ bản. Điều này ngăn chặn các câu lệnh xem dữ liệu không nhất quán do các giao dịch đồng thời cập nhật cùng một hàng, cung cấp sự cô lập giao dịch cho mỗi phiên cơ sở dữ liệu. Không giống như các phương pháp khóa truyền thống, MVCC giảm thiểu tranh chấp khóa, cho phép hiệu suất đa người dùng hợp lý.

Khi xóa một hàng trong các hệ thống MVCC như PostgreSQL, hàng đó không bị xóa ngay lập tức khỏi các trang dữ liệu (data pages). Thay vào đó, nó được đánh dấu là đã xóa hoặc đã hết hạn đối với giao dịch hiện tại nhưng vẫn hiển thị với các giao dịch đang xem một snapshot cũ hơn, tránh xung đột. Khi các giao dịch hoàn tất, các tuple chết hoặc hết hạn này dự kiến sẽ được vacuum và không gian sẽ được thu hồi. Trong PostgreSQL, một thao tác UPDATE tương đương với sự kết hợp của DELETE và INSERT. Khi một hàng được cập nhật, PostgreSQL đánh dấu phiên bản cũ là đã hết hạn (giống như một DELETE) nhưng vẫn giữ cho nó hiển thị với các snapshot giao dịch cũ hơn. Sau đó, nó chèn một phiên bản mới của hàng với các giá trị được cập nhật (giống như một INSERT). Theo thời gian, các phiên bản hàng đã hết hạn sẽ tích tụ cho đến khi quy trình VACUUM loại bỏ chúng, thu hồi lại không gian. Cách tiếp cận này cho phép mô hình MVCC của PostgreSQL, cung cấp sự cô lập snapshot mà không cần khóa rõ ràng trong quá trình cập nhật.

Autovacuum của PostgreSQL là một quy trình bảo trì tự động giúp thu hồi bộ nhớ bị chiếm dụng bởi các tuple chết và cập nhật các thống kê được bộ lập kế hoạch truy vấn (query planner) sử dụng. Quy trình autovacuum sẽ kích hoạt khi tuổi tối đa (tính bằng số giao dịch) vượt qua `autovacuum_freeze_max_age`, hoặc khi đạt đến ngưỡng: `autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * số lượng tuple`. Trong công thức này, `autovacuum_vacuum_threshold` đại diện cho số lượng tối thiểu các tuple được cập nhật hoặc xóa cần thiết để bắt đầu dọn dẹp, trong khi `autovacuum_vacuum_scale_factor` là một phần kích thước của bảng được thêm vào phép tính ngưỡng để xác định khi nào việc bảo trì nên diễn ra. Nếu autovacuum không dọn dẹp được các tuple chết vì một số lý do nhất định, bạn có thể cần phải xử lý các bảng bị phình to nghiêm trọng theo cách thủ công.

Các tuple chết được lưu trữ cùng với các tuple sống trong các trang dữ liệu. Sự phình to cũng có thể là do không gian trống trong các trang, ví dụ như sau khi autovacuum đã dọn dẹp các tuple chết. Trong quá trình thực thi truy vấn, PostgreSQL quét nhiều trang hơn chứa đầy các tuple chết, gây ra I/O tăng và các truy vấn chậm hơn. Các bảng bị phình to nghiêm trọng làm cho khối lượng công việc của cơ sở dữ liệu tiêu thụ I/O đọc không cần thiết, ảnh hưởng đến hiệu suất ứng dụng. Việc dọn dẹp sự phình to có thể là cần thiết nếu autovacuum thất bại.

Trước khi chúng ta đi sâu vào việc phân tích sự phình to của bảng với pgstattuple, hãy đảm bảo bạn đã thiết lập mọi thứ để có thể làm theo. Bạn sẽ cần quyền truy cập vào một phiên bản Amazon RDS hoặc Aurora PostgreSQL, cũng như một máy khách đã cài đặt psql và được cấu hình đúng cách để kết nối với cơ sở dữ liệu của bạn. Hãy chắc chắn rằng bạn có các quyền cần thiết để tạo bảng và cài đặt các phần mở rộng trong môi trường PostgreSQL của mình. Đối với phần trình diễn này, chúng tôi sẽ sử dụng bảng `pgbench_accounts`. Nếu bạn chưa có bảng này, bạn có thể dễ dàng tạo nó bằng tiện ích pgbench. Chạy lệnh `pgbench -i -s 10` để khởi tạo một lược đồ pgbench với hệ số tỷ lệ là 10, điều này sẽ tạo ra bảng `pgbench_accounts` cùng với các bảng cần thiết khác. Điều này sẽ cung cấp cho chúng ta dữ liệu mẫu để làm việc trong quá trình phân tích. Ngoài ra, bạn nên cài đặt phần mở rộng pgstattuple trên phiên bản cơ sở dữ liệu của mình. Nếu bạn chưa cài đặt nó, bạn có thể làm như vậy bằng cách chạy `CREATE EXTENSION pgstattuple;` với tư cách là người dùng có đủ đặc quyền. Với các điều kiện tiên quyết này, bạn sẽ sẵn sàng khám phá phân tích sự phình to của bảng bằng cách sử dụng dữ liệu thực trong một môi trường được kiểm soát.

Mặc dù pgstattuple cung cấp phân tích toàn diện về sự phình to của bảng, nó có thể tiêu tốn nhiều tài nguyên. Chúng tôi khuyên bạn nên sử dụng trước các truy vấn ước tính sự phình to nhẹ hơn được ghi nhận tại đây. Nếu cần phân tích chi tiết hơn, đây là cách sử dụng pgstattuple. Ví dụ sau đây minh họa cách sử dụng pgstattuple để phân tích thông tin về sự phình to trong một bảng.

Tạo bảng pgbench_accounts_test với 10,000 bản ghi:
![](/images/2-Proposal/Blog3_1.png)

Trong ví dụ này, truy vấn pgstattuple trả về số lượng tuple chết là 0 và kích thước bảng là 1672kB:
![](/images/2-Proposal/Blog3_2.png)

Để chứng minh cách sử dụng pgstattuple, chúng tôi tắt tính năng tự động hút chân không (không khuyến khích trong môi trường sản xuất) và cập nhật 2.500 bản ghi:
![](/images/2-Proposal/Blog3_3.png)

Bây giờ, dữ liệu pgstattuple của bảng này cho thấy 2.500 bộ dữ liệu phiên bản cũ được chuyển sang bộ dữ liệu đã chết.
![](/images/2-Proposal/Blog3_4.png)

**bloat_percentage** trong PostgreSQL đề cập đến tỷ lệ không gian có thể được thu hồi trong một bảng hoặc chỉ mục so với tổng kích thước của nó. Nó có thể được tính toán bằng cách sử dụng dữ liệu từ pgstattuple như sau:
![](/images/2-Proposal/Blog3_5.png)

Giá trị bloat_percentage vượt quá 30%–40% thường cho thấy tình trạng bloat có vấn đề cần được xử lý. Để dọn dẹp bloat, hãy sử dụng lệnh VACUUM:
![](/images/2-Proposal/Blog3_6.png)

Chúng ta hãy kiểm tra dữ liệu pgstattuple sau thao tác VACUUM:
![](/images/2-Proposal/Blog3_7.png)

Thao tác **VACUUM** sẽ đặt lại `dead_tuple_count` về 0. Không gian trống vẫn còn gắn liền với bảng sẽ có sẵn cho các thao tác chèn (**INSERT**) hoặc cập nhật (**UPDATE**) trong cùng một bảng. Điều này làm cho `table_len` (độ dài bảng) không thay đổi ngay cả sau khi thực hiện thao tác VACUUM.

Để thu hồi dung lượng đĩa bị chiếm dụng bởi sự phình to (bloat), có hai lựa chọn:

* **VACUUM FULL** – **VACUUM FULL** có thể thu hồi nhiều dung lượng đĩa hơn nhưng chạy chậm hơn nhiều. Nó yêu cầu một khóa `ACCESS EXCLUSIVE` (khóa truy cập độc quyền) trên bảng mà nó đang xử lý, và do đó không thể thực hiện song song với các hoạt động sử dụng khác của bảng. Mặc dù các thao tác **VACUUM FULL** thường không được khuyến khích trong môi trường sản xuất (production), chúng có thể chấp nhận được trong các cửa sổ bảo trì đã được lên lịch, nơi thời gian chết (downtime) đã được lên kế hoạch và phê duyệt.

* **pg_repack** – **pg_repack** là một phần mở rộng của PostgreSQL giúp loại bỏ hiệu quả sự phình to của bảng và chỉ mục trong khi vẫn duy trì tính sẵn sàng trực tuyến (online). Không giống như `CLUSTER` và `VACUUM FULL`, nó giảm thiểu thời gian khóa độc quyền trong quá trình xử lý, mang lại hiệu suất tương đương với `CLUSTER`. Mặc dù **pg_repack** cho phép sắp xếp lại bảng và chỉ mục trực tuyến với thời gian chết của ứng dụng ở mức tối thiểu, điều quan trọng là phải xem xét các hạn chế của nó. Phần mở rộng này vẫn yêu cầu các khóa độc quyền ngắn trong quá trình hoạt động và có thể gặp khó khăn để hoàn thành trên các bảng có giao dịch tốc độ cao, có khả năng ảnh hưởng đến hiệu suất cơ sở dữ liệu. Đối với các bảng được sử dụng nhiều, nơi việc đóng gói lại toàn bộ (full repacking) gặp khó khăn, hãy xem xét phương án thay thế là chỉ đóng gói lại chỉ mục (index-only repacking). Các phương pháp hay nhất bao gồm kiểm thử kỹ lưỡng trong môi trường không phải sản xuất, lập lịch vào các khoảng thời gian có lưu lượng truy cập thấp, và có sẵn một kế hoạch giám sát và khôi phục (rollback). Mặc dù có những lợi ích, người dùng nên nhận thức được những rủi ro tiềm ẩn và lập kế hoạch phù hợp khi triển khai **pg_repack** trong môi trường PostgreSQL của họ.
Hoạt động VACUUM FULL giảm table_len:

![](/images/2-Proposal/Blog3_8.png)

Thao tác VACUUM FULL sẽ lấy lại dung lượng bị lãng phí vào bộ nhớ đĩa và giảm table_len. Truy vấn sau đây xác định độ phình của 10 bảng lớn nhất trong cơ sở dữ liệu của bạn bằng pgstattuple. pgstattuple thực hiện quét toàn bộ bảng và có thể tiêu tốn nhiều tài nguyên của phiên bản như CPU ​​và I/O hơn. Điều này làm cho hoạt động của pgstattuple chậm hơn đối với các bảng lớn. Ngoài ra, hàm pgstattuple_approx(relation) cung cấp ước tính nhanh hơn về các số liệu này. Mặc dù ít tốn tài nguyên hơn pgstattuple, nhưng nó vẫn có thể gây khó khăn cho các bảng rất lớn hoặc hệ thống bận rộn. Hãy cân nhắc chạy vào giờ thấp điểm hoặc trên một bản sao nếu có.

## Tự động hóa việc vacuum thủ công
Việc thường xuyên theo dõi sự phình to (bloat) cho phép bạn chủ động xác định các nhu cầu bảo trì, trước khi hiệu suất bị ảnh hưởng. Các số liệu về sự phình to cũng có thể giúp tinh chỉnh các cài đặt autovacuum để dọn dẹp không gian một cách quyết liệt hơn nếu cần. Sau khi bạn xác định 10 bảng bị phình to nhiều nhất, bạn có thể tự động hóa thao tác VACUUM bằng cách sử dụng phần mở rộng pg_cron. pg_cron là một trình lập lịch công việc dựa trên cron cho PostgreSQL, chạy bên trong cơ sở dữ liệu như một phần mở rộng. Nó sử dụng cú pháp tương tự như cron thông thường, nhưng cho phép bạn lập lịch các lệnh PostgreSQL trực tiếp từ cơ sở dữ liệu. Đoạn mã sau đây là một ví dụ về việc sử dụng hàm cron.schedule của pg_cron để thiết lập một công việc chạy VACUUM trên một bảng cụ thể vào lúc 23:00 (GMT) hàng ngày:
![](/images/2-Proposal/Blog3_9.png)
 ## Chẩn đoán và giải quyết tình trạng phình to của chỉ mục
Giống như bảng, các chỉ mục (index) trong PostgreSQL cũng có thể bị phình to, gây lãng phí không gian và ảnh hưởng đến hiệu suất. pgstattuple cho phép phát hiện tình trạng phình to của chỉ mục bằng cách sử dụng pgstatindex.

Truy vấn sau đây hiển thị mã định danh của chỉ mục, tổng kích thước chỉ mục tính bằng byte và mật độ lá trung bình (average leaf density):

![](/images/2-Proposal/Blog3_10.png)

Mật độ lá trung bình là tỷ lệ phần trăm dữ liệu hữu ích trong các trang lá (leaf pages) của chỉ mục. Các chỉ mục bị phình to đáng kể có thể được xây dựng lại bằng lệnh REINDEX hoặc pg_repack để loại bỏ không gian chết và khôi phục hiệu suất tối ưu. Khuyến nghị nên kiểm tra định kỳ tình trạng phình to đối với các chỉ mục bận rộn, có tỷ lệ thay đổi cao.

## Đánh giá sự phân mảnh của chỉ mục
Một công dụng giá trị khác của pgstattuple là xác định các vấn đề về phân mảnh chỉ mục. Sự phân mảnh xảy ra khi các trang chỉ mục (index pages) trở nên rải rác do các thao tác xóa, cập nhật và chia tách trang (page splits). Các chỉ mục bị phân mảnh nhiều có nhiều tuple chết chiếm dụng không gian một cách không hiệu quả.
Chúng ta có thể kiểm tra mức độ phân mảnh bằng cách sử dụng leaf_fragmentation:
![](/images/2-Proposal/Blog3_11.png)

Nếu leaf_fragmentation cao, chỉ mục có khả năng đã bị phân mảnh và nên xem xét việc thực hiện REINDEX. Việc xây dựng lại sẽ loại bỏ sự phân mảnh và các chi phí hiệu suất liên quan.

## Các phương pháp hay nhất khi sử dụng pgstattuple

Hãy xem xét các phương pháp hay nhất sau đây khi sử dụng **pgstattuple** để giám sát và bảo trì PostgreSQL:

* Để ước tính sự phình to (bloat) trong các bảng PostgreSQL, hãy sử dụng truy vấn `check_postgres` được đề cập trên wiki của PostgreSQL.
* Sử dụng phần mở rộng `pgstattuple` để phân tích lưu trữ vật lý của các bảng cơ sở dữ liệu, cung cấp các thống kê chi tiết về việc sử dụng không gian trong cơ sở dữ liệu, bao gồm cả lượng không gian bị lãng phí do phình to.
* Xây dựng lại các bảng và chỉ mục bị phình to đáng kể để thu hồi không gian chết.
* Theo dõi chỉ số `dead_tuple_percent` cao để xác định các vấn đề về phân mảnh.
* Tập trung bảo trì vào các bảng và chỉ mục quan trọng đối với hiệu suất của khối lượng công việc.
* Tránh chạy `pgstattuple` trên các bảng có hoạt động cao để ngăn chặn sự can thiệp.
* Sử dụng các số liệu của `pgstattuple` để tinh chỉnh cài đặt `autovacuum`.
* Kết hợp `pgstattuple` với phân tích truy vấn và nhật ký (logs) để có cái nhìn toàn diện về cơ sở dữ liệu.

## Kết luận

Phần mở rộng **pgstattuple** đóng vai trò như một công cụ mạnh mẽ để khám phá các số liệu chẩn đoán quan trọng trong cơ sở dữ liệu PostgreSQL, tiết lộ các thống kê lưu trữ chi tiết giúp các nhóm xác định và giải quyết các vấn đề ảnh hưởng đến hiệu suất như phình to và phân mảnh chỉ mục. Hoạt động liền mạch với Aurora và RDS PostgreSQL, phần mở rộng này cung cấp khả năng hiển thị cần thiết về các mẫu lưu trữ và yêu cầu bảo trì.

Việc tuân theo các phương pháp hay nhất của pgstattuple là chìa khóa để duy trì cơ sở dữ liệu PostgreSQL hiệu quả, hiệu suất cao, và các tổ chức có thể nâng cao hơn nữa việc quản lý cơ sở dữ liệu của mình thông qua các tùy chọn hỗ trợ của AWS – các khách hàng của **AWS Enterprise Support**, **Enterprise On-Ramp**, và **Business Support** có thể tận dụng các cam kết **AWS Countdown Premium** để được hướng dẫn tối ưu hóa, cho phép các nhóm tự tin triển khai các phương pháp hay nhất và duy trì hiệu suất tối ưu trong khi tập trung vào các mục tiêu kinh doanh cốt lõi của họ.

## Thông tin tác giả

### Vivek Singh
Vivek Singh là Chuyên gia Cơ sở dữ liệu Chính, Quản lý Tài khoản Kỹ thuật tại AWS, tập trung vào Amazon RDS cho PostgreSQL và các công cụ Amazon Aurora PostgreSQL. Ông làm việc với các khách hàng doanh nghiệp, cung cấp hỗ trợ kỹ thuật về hiệu suất vận hành PostgreSQL và chia sẻ các phương pháp hay nhất về cơ sở dữ liệu. Ông có hơn 17 năm kinh nghiệm trong các giải pháp cơ sở dữ liệu nguồn mở và rất thích làm việc với khách hàng để giúp thiết kế, triển khai và tối ưu hóa khối lượng công việc cơ sở dữ liệu quan hệ trên AWS.

### Kiran Singh
Kiran Singh là Kiến trúc sư Giải pháp Đối tác Cấp cao và là chuyên gia về Amazon RDS và Amazon Aurora tại AWS, tập trung vào cơ sở dữ liệu quan hệ. Cô giúp khách hàng và đối tác xây dựng các giải pháp được tối ưu hóa cao, có khả năng mở rộng và bảo mật; hiện đại hóa kiến trúc của họ; và di chuyển khối lượng công việc cơ sở dữ liệu của họ sang AWS.

### Sagar Patel
Sagar Patel là Kiến trúc sư Chuyên ngành Cơ sở dữ liệu Chính của nhóm Dịch vụ Chuyên nghiệp tại Amazon Web Services. Ông làm việc với tư cách là chuyên gia di chuyển cơ sở dữ liệu, cung cấp hướng dẫn kỹ thuật và hỗ trợ khách hàng Amazon di chuyển cơ sở dữ liệu tại chỗ của họ sang AWS.



