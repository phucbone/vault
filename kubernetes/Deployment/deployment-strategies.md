# Các chiến lược Deployment

Cách deploy sẽ ảnh hưởng trực tiếp tới sự ổn đinh của application và các bạn sẽ phải lựa chọn phù thuộc vào yêu cầu:
- Nếu cần Zero Downtime, các bạn có thể xem xét `Rolling Update Deployment` hoặc `Blue/Green Deployment`
- Nếu cần test với Real Traffic từ user trong trường hợp bạn không chắc chắn thì có thể sử dụng `Canary Test`
- Nếu cần so sánh hiệu quả giữa các implementation thì có thể xem xét `A/B Test`

## Recreate Deployment

Ưu điểm|Nhược điểm|Lưu ý
---|---|---
Setup khá đơn giản|Downtime|Tránh sử dụng với workload, task quan trọng
Không cần quan tâm Backward-Compatibility|-|Không sử dụng với các hệ thống SLAs

## Rolling Update Deployment (Ramp-up Deployment)

Ưu điểm|Nhược điểm|Lưu ý
---|---|---
Zero Downtime|Rollback chậm khi xảy ra lỗi|Application phải hỗ trợ Backward-Compatibility (Hỗ trợ cả version1 và version2 chạy song song)
Giảm rủi ro|-|Sticky session + Connection draining

## Blue/Green Deployment

Ưu điểm|Nhược điểm|Lưu ý
---|---|---
Zero Downtime|Tốn tiền khi phải chi cho cả resource Blue và Green|Application phải hỗ trợ Backward-Compatibility (Hỗ trợ cả version1 và version2 chạy song song)
Rollback không tốn thời gian|Quá trình vận hành phức tạp hơn các cách deploy khác|Sticky session + Connection draining
Giảm thiểu rủi ro trong quá trình deploy|-|-

## Canary Test (Canary Deployment)

Ưu điểm|Nhược điểm|Lưu ý
---|---|---
Zero Downtime|Quá trình deploy tốn thời gian|Application phải hỗ trợ Backward-Compatibility (Hỗ trợ cả version1 và version2 chạy song song)
Rollback đơn giản (điều chỉnh LB để hướng 100% traffic quay lại version1)|-|Sticky session + Connection draining
Test version mới của Application với Production Traffic|-|-

## A/B Test

Ưu điểm|Nhược điểm|Lưu ý
---|---|---
Test version mới của Application với Production Traffic|Quá trình deploy tốn thời gian (xem xét, tổng hợp, nghiên cứu, đánh giá kết quả)|Application phải hỗ trợ Backward-Compatibility (Hỗ trợ cả version1 và version2 chạy song song)
-|-|Cần chuẩn bị công cụ để xem xét, tổng hợp, nghiên cứu, đánh giá kết quả

## Shadow Deployment

Ưu điểm|Nhược điểm|Lưu ý
---|---|---
Test version mới của Application với Production Traffic|Quá trình deploy tốn thời gian (xem xét, tổng hợp, nghiên cứu, đánh giá kết quả)|Application phải hỗ trợ Backward-Compatibility (Hỗ trợ cả version1 và version2 chạy song song)
Trong quá trình test không làm ảnh hưởn tới End-User|-|Quản lý môi trường của version2
Giảm thiểu rủi ro trong quá trình deploy|-|Cần phải có implement, logic để bốc request từ version1 gửi về version2
-|-|Nếu application có kết nối với 3rd-party-services, api hay thay đổi stage thì cần phải lưu ý
