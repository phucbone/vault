![[cicd-circleci-presend.jpg]](https://github.com/phucbone/vault/blob/master/imgs/imgs-cicd/imgs-ci/imgs-circleci/cicd-circleci-presend.jpg?raw=true)

# Giới thiệu

`CircleCI` là một công cụ `Continuous Integration` được thiết kế với tốc độ cao và có khả năng mở rộng

Cấu hình CI được viết dưới dạng `YAML` file, giúp việc theo dõi các thay đổi cấu hình CI dễ dàng hơn. 

Hiện nay `CircleCI` là một trong những công cụ `Continuous Integration` được đánh giá tốt.

Ưu điểm|Nhược điểm|Chi phí
---|---|---
Giao diện người dùng đẹp, môi trường xây dựng linh hoạt, dễ sử dụng|Rủi ro lớn nhất khi chọn `CircleCI` là hay bị downtime ngắn và đôi lúc rất chậm ở mặt trải nghiệm, nếu một Job của ta cần thời gian chạy rất lâu mà nó bị downtime một phát thi ta phải chạy lại từ đầu, rất tốn thời gian|Nếu quy mô của chúng ta nhỏ thì có thể chọn gói Free, nếu quy mô khá lớn một chút thì ta có thể chọn gói Performance
Hỗ trợ phân quyền theo kiểu role-based access control (RBAC)|Có rất nhiều hạn chế với Docker|Nếu phần mền của ta yêu cầu phải cập nhật liên tục thì `CircleCI` phải chạy liên tục, nếu `CircleCI` thực thi càng nhiều thì sẽ càng tốn phí, giống với `AWS CodeBuild` (the more resources you use, the more you pay), nên nó không thích hợp với các dự án quy mô lớn và cần cập nhật liên tục
Hỗ trợ nhiều môi trường build khác nhau|Đội lúc một Job mất rất nhiều thời gian để được thực thi|
