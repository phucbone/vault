![[postgresql-presend.png]](https://github.com/phucbone/vault/blob/master/imgs/imgs-database/imgs-posgressql/postgresql-presend.png?raw=true)

---
# Giới thiệu

`PostgreSQL` là một hệ thống quản trị cơ sở dữ liệu quan hệ và đối tượng (object-relational database management system) miễn phí và nguồn mở (RDBMS) tiên tiến nhất hiện nay.

Khả năng mở rộng cao và tuân thủ các tiêu chuẩn kỹ thuật.

Nó được thiết kế để xử lý một loạt các khối lượng công việc lớn, từ các máy tính cá nhân đến kho dữ liệu hoặc dịch vụ Web có nhiều người dùng đồng thời.

-   Được phát triển bởi PostgreSQL Global Development Group, Phát hành lần đầu: 08/07/1996
-   Linh động có thể chạy được trên nhiều nền tảng khác nhau như Mac OS X, Solaris và Windows.
-   Là một phần mềm mã nguồn mở miễn phí bởi vậy PostgreSQL có thể được dùng, sửa đổi và phổ biến bởi bất kỳ ai cho bất kỳ mục đích nào.
-   Có tính ổn định cao.
-   Là hệ thống quản lý cơ sở dữ liệu đầu tiên triển khai tính năng kiểm soát đồng thời nhiều phiên bản (MVCC).

---
# Tính năng

`PostgreSQL` tích hợp nhiều tính năng tuyệt vời giúp hỗ trợ nhà phát triển xây dựng app đáp ứng các chức năng phức tạp, truy vấn nhanh chóng và bảo mật duy trì tính toàn vẹn và độ tin cậy.

Để đáng tin cậy hơn, `PostgreSQL` cung cấp các tùy chọn bảo mật, xác thực và khôi phục thảm họa khác nhau.

`PostgreSQL` được chứng minh là có khả năng mở rộng cao cả về số lượng dữ liệu và số lượng người dùng có thể thao tác cùng lúc.

## Tính năng nổi bật
-   Câu truy vấn phức hợp (complex query)
-   Thủ tục sự kiện (trigger)
-   Các khung nhìn (view)
-   Tính toàn vẹn của các giao dịch (integrity transactions)
-   Việc kiểm tra truy cập đồng thời đa phiên bản (multiversion concurrency control)
-   Truy vấn xử lý song song (parallel query)
-   Sao chép dữ liệu dạng luồng (Streaming replication)

## Kiểu dữ liệu
-   Nguyên hàm: Số nguyên, số, chuỗi, Boolean
-   Cấu trúc: Date/Time, Array, Phạm vi, UUID
-   Document: JSON/JSONB, XML, Key-value (Hstore)
-   Hình học: Điểm, Đường thẳng, Vòng tròn, Đa giác
-   Tùy chỉnh: Composite, Các kiểu tùy chỉnh

## Toàn vẹn dữ liệu
-   UNIQUE, NOT NULL
-   Primary Keys
-   Foreign Keys
-   Ràng buộc loại trừ
-   Khóa hàm số, Khóa khuyến nghị
-   Đồng quy, hiệu suất
-   Lập danh mục: B-tree, Multicolumn, Expressions, Partial
-   Lập danh mục nâng cao: GiST, SP-Gist, KNN Gist, GIN, BRIN, Bloom filters
-   Trình lập kế hoạch / trình tối ưu hóa truy vấn phức tạp, quét index-only, thống kê số liệu trên nhiều cột.
-   Giao tác, Giao tác dạng nest (thông qua lưu điểm)
-   Điều khiển đồng thời nhiều phiên bản (MVCC)
-   Truy vấn đọc song song
-   Phân vùng bảng
-   Tất cả các mức độ giao dịch độc lập được xác định trong tiêu chuẩn SQL, bao gồm cả Serializable
-   Độ tin cậy, phục hồi sau thảm hoạ
-   Ghi nhật ký ghi trước (Write-ahead Logging - WAL)
-   Replication: Không đồng bộ, Đồng bộ, Logical
-   Khôi phục điểm-theo-thời gian (Point-in-time-recovery - PITR), active standbys
-   Không gian bảng

## Bảo mật
-   Xác thực: GSSAPI, SSPI, LDAP, SCRAM-SHA-256, Certificate và các hình thức khác
-   Hệ thống kiểm soát truy cập mạnh mẽ
-   Bảo mật cấp độ cột và hàng

## Khả năng mở rộng
-   Phương pháp lưu trữ
-   Ngôn ngữ thủ tục: PL / PGSQL, Perl, Python (và nhiều ngôn ngữ khác)
-   Trình wrapper dữ liệu ngoài: kết nối với các cơ sở dữ liệu hoặc luồng khác với giao diện SQL chuẩn
-   Và nhiều tiện ích mở rộng cung cấp chức năng bổ sung, bao gồm cả PostGIS
-   Tìm kiếm văn bản:
-   Hỗ trợ các bộ ký tự quốc tế, ví dụ: thông qua ICU collations
-   Tìm kiếm văn bản đầy đủ

---
# Vai trò

`PostgreSQL` là một hệ thống quản trị dữ liệu mở dành cho các doanh nghiệp.

Hệ thống quản lý này tương thích với nhiều nền tảng khác nhau, sử dụng được đa dạng ngôn ngữ và phần mềm trung gian phổ biến hiện nay.

Bởi vậy, `PostgreSQL` được áp dụng nhiều trong các ngành dữ liệu GIS của chính phủ, tài chính ngân hàng, sản xuất – kinh doanh, công nghệ web và các công việc thu thập dữ liệu khoa học.

`PostgreSQL` là mã nguồn mở miễn phí. Bởi vậy, không cần trả bất cứ chi phí gì khi sử dụng dịch vụ này.

Tuy nhiên, hệ quản trị cơ sở dữ liệu `PostgreSQL` lại không thuộc sở hữu của bất kỳ tổ chức nào cũng là một nhược điểm. Người dùng khó khăn khi đưa tên mình ra khỏi đó mặc dù có đủ các tính năng như hệ thống DBMS khác.

---
# So sánh

## PostgresSQL vs MySQL

![[postgresql-vs-mysql.png]](https://github.com/phucbone/vault/blob/master/imgs/imgs-database/imgs-posgressql/postgresql-vs-mysql.png?raw=true)

Đặc điểm|PostgresSQL|MySQL
---|---|---
Non-blocking trong DDL|PostgreSQL sẽ tùy thuộc vào những cậu lệnh thực thi thao tác DDL nào. Thao tác write, chẳng hạn như thêm cột, nó sẽ phát sinh block đến bảng, khiến bạn không thể tham chiếu thêm nữa. Có thể thấy, PostgreSQL thao tác DDL là Alter table không phải non – blocking. Để sử dụng được, bạn cần sự hỗ trợ của các tool. Ví dụ như pg_repack, tool này chuyên dùng cho maintain, giúp thực thi các thao tác alter table một phần hoặc tiến hành reindex bằng cách block tối thiểu.|Thực thi thao tác nhiều DDL bằng Non – blocking (Tức là, dù trong transaction nhưng không có block đến bảng). Chức năng Non – Blocking có từ phiên bản MySQL 5.6. Trong trường hợp Alter table (thay đổi, điều chỉnh cột, hàng,… trong bảng), chỉ những cột đối tượng cần xử lý thì không tạo bảng từ con số không. Bởi vậy, tốc độ xử lý nhanh, kéo thiểu việc giảm tải cho máy chủ server
Performance của DML (thao tác dữ liệu)|Câu SELECT cần phải sort lượng dữ liệu lớn sau khi ORDER BY thì PostgreSQL sẽ làm việc nhanh hơn. Chậm hơn. PostgreSQL khi update sẽ xử lý tương tự như insert. Tức là nó sẽ đánh dấu flag như delete vào dòng trước khi thay đổi, rồi thêm dòng mới có dữ liệu sau khi thay đổi. Delete nhanh hơn.|MySQL có thuật toán sort không tốt bằng PostgreSQL. Bởi vậy MySQL sẽ bị chậm hơn. MySQL chuyên về use case. Ví dụ, lấy da 10 hay 100 dữ liệu đầu tiền (như cách của Twitter) sẽ nhanh hơn so với PostgreSQL. UPDATE thì performance của MySQL tốt hơn so với PostgreSQL. Bởi MySQL ghi đè đối tượng được update, đúng nghĩa cập nhật. MySQL delete chậm do sau khi xóa dữ liệu nó sẽ thực hiện đánh lại secondary index xử lý đồng bộ nên tốn thời gian hơn. Từ phiên bản 5.5 trở lên sẽ cải thiện tình trạng này tốt hơn.
Thuật toán JOIN|PostgreSQL support cả 3 loại Hash Join, Nested Loop Join, Sort Merge Join. Trong đó: Sử dụng dữ liệu cần join nhiều thì dùng Hash Join và Sort Merge join. Dữ liệu đã được sort thì sử dụng Sort Merge Join. Dữ liệu của các bảng được Join ít hoặc bảng ít bảng nhiều, bạn có thể dùng Nested Loop Join.|MySQL chủ yếu sử dụng thuật toán Support Nested Loop Join, bởi hệ quản trị cơ sở dữ liệu này thiết kế đúng theo tiêu chí thuật toán đơn giản.
Xử lý transaction|PostgreSQL có tính năng Read – committed, hình thức này có khả năng xảy ra vấn đề phantom-read hoặc non – repeatable – read (hiện tượng cùng một transaction đọc cùng dữ liệu mà giá trị thay đổi). Trường hợp đổi sang Repeatable – read sẽ không có next key locking nên tránh được tình trạng phantom – read. Bởi vậy PostgreSQL dễ tránh conflict ngon hơn MySQL.|MySQL mặc định xử lý transaction là repeatable-read. Phương thức này giúp giữ nguyên dữ liệu đọc, không lo thay đổi, mất ở một transaction khác. Trong trường hợp thêm dữ liệu bởi transaction khác sẽ được thấy khi chạy transaction với phantom-read. Để cải thiện tình trạng này, khi sử dụng MySQL cần dùng Next Key Locking.
Store procedure, Trigger|PostgreSQL còn sử dụng được cả procedure bên ngoài như python.|MySQL chỉ sử dụng được SQL. Trên mỗi SQL không thực thi step của store procedure được. Bản MySQL 5.6 có điểm yếu là mỗi một bảng chỉ gán được tối đa 6 trigger. Nếu không thì chúng chỉ có for each row.
Dạng logic và vật lý của replication|PostgreSQL chỉ có dạng vật lý ( copy image dòng sau khi thay đổi). Nhưng từ phiên bản release beta version 10 có thể sử dụng được cả dạng logic (copy câu SQL).|MySQL có replication dạng logic và vật lý. Dạng logic được setting mặc định. Từ phiên bản 5.7 về sau thì dạng vật lý là mặc định.
Chức năng tiện lợi|PostgreSQL có hàm window, có thể apply hàm tổng, hợp thành set kết quả và phân chia thành từng phần. Mệnh đề With có thể subquery trước khi select. Bởi vậy, chức năng tiện lợi của postgreSQL mạnh hơn. Chức năng Query song song giúp tăng tốc độ xử lý bằng cách sử dụng nhiều CPU để chạy query. PostgreSQL có đa dạng các tool từ OSS, PostGIS,… Chức năng Pg_basebackup có thể sử dụng online hoặc dùng trên remote đều được.|MySQL chưa có chức năng này. MySQL thì có backup vật online còn Xtrabackup hoặc enterprise backup  không thực hiện được base backup online hay trên remote.

---
# Danh sách

No|Content
---|---
1|[[install-postgre-sql]](https://github.com/phucbone/vault/blob/master/database/PosgreSQL/install-postgre-sql.md)

