
`CI/CD` là viết tắt của (`Continuous Integration` and `Continuous Delivery`/`Continuous Deployment`). 

Giải thích đơn giản `CI/CD` là một nền tảng mà tập họp các công cụ giúp nhà phát triển, kỹ sư, và DevOps đóng gói và phân phối phần mền tới người dùng một cách tự động và dễ dàng nhất.

Một nền tảng [[Continuous Integration]](https://github.com/phucbone/vault/blob/master/cicd/ci/Continuous%20Integration.md) [[Continuous Deployment]](https://github.com/phucbone/vault/blob/master/cicd/cd/Continuous%20Deployment.md) sẽ bao gồm 3 mô-đun:

-   `Continuous Integration`: tích hợp sự thay đổi của code vào phiên bản hiện tại
-   `Continuous Delivery`: triển khai sự thay đổi của phần mền lên production một cách an toàn, ví dụ triển khai phiên bản Canary Release
-   `Continuous Deployment`: thực hiện triển khai sản phẩm lên production.

Sự khác biệt của `Continuous Delivery` với `Continuous Deployment`

Continuous Delivery|Continuous Delivery
---|---
Sẽ triển khai phiên bản pre-release của sản phẩm, yêu cầu ta kiểm thử mọi thứ đều ổn thì mới tiến hành triển khai|sẽ triển khai thẳng lên production luôn.
