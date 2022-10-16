![[cicd-aws-codebuild-presend.jpg]](https://github.com/phucbone/vault/blob/master/imgs/imgs-cicd/imgs-ci/imgs-concourse/cicd-aws-codebuild-presend.jpg?raw=true)

# Giới thiệu

Nếu bạn là một DevOps mà đang sử dụng `AWS` thì sử dụng cộng cụ `Continuous Integration` của `AWS` phát triển luôn là một lựa chọn tốt nhất.

`AWS CodeBuild` là công cụ `Continuous Integration` mà `AWS` phát triển, có thể tích hợp với rất nhiều dịch vụ khác của `AWS`.

Sử dụng `AWS CodeBuild` sẽ giúp công việc triển khai trên `AWS` dễ dàng hơn, với `AWS CodeBuild` ta không cần phải tự quản lý việc mở rộng build servers, `AWS` sẽ làm việc đó.

Ưu điểm|Nhược điểm|Chi phí
---|---|---
Có thể kết hợp với CloudWatch để lưu logs không giới hạn và có thể giám sát quá trình build|Nhược điểm lớn nhất của CodeBuild là giao diện sử dụng không đẹp lắm|AWS CodeBuild tính tiền theo từng phút build, thay vì phải xây dựng cả một server build và trả tiền 24/7 kể cả lúc không cần build.
Có thể build song song cả trăm dự án với tính năng tự động mở rộng của CodeBuild|Không linh hoạt hoặc có thể tùy chỉnh|Với CodeBuild thì khi nào thực hiện build mới phải trả tiền sẽ giúp tiết kiệm rất nhiều chi phí cho server, mỗi tháng AWS sẽ miễn phí cho 100 phút build.
Hỗ trợ Docker-base, có sẵn docker command mà không cần phải tự cài|Thiết lập ban đầu phức tạp|Tuy nhiên đối với một dự án mà thời gian build lâu và liên tục thì AWS CodeBuild rất tốn tiền.
YAML-based|
