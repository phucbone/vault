![[hashicorp-nomad.jpg]](https://github.com/phucbone/vault/blob/master/imgs/imgs-hashicorp-stack/imgs-hashicorp-nomad/hashicorp-nomad.jpg?raw=true)

# Giới thiệu

`Nomad` được phát hành bởi `Hashicorp`, là một công cụ phổ biến trong việc quản lý và điều phối workload, một workload có thể là containerized hoặc non-containerized.

`Containerized` là các ứng dụng được đóng gói thành container, ví dụ như đóng gói ứng dụng nodejs thành container image bằng docker và dùng kubernetes để chạy container đó.

`Non-containerized` là các ứng dụng chạy thẳng trên server của ta mà không cần phải được đóng gói thành container để chạy, ví dụ như thay vì đóng gói ứng dụng nodejs thành container thì ta có thể ssh thẳng lên server, cài nodejs lên server và chạy ứng dụng bằng pm2.

![[hashicorp-nomad-2.png]](https://github.com/phucbone/vault/blob/master/imgs/imgs-hashicorp-stack/imgs-hashicorp-nomad/hashicorp-nomad-2.png?raw=true)

Là một trong những tools cạnh tranh với `Kubernetes`.

Khi so sánh với `Kubernetes` ở phần mở rộng thì `Nomad` hoàn toàn chiếm ưu thế hơn: 

Tool|Kubernetes|Nomad
---|---|---
Simplicity|`Kubernetes` được thiết kế 4 thành phần chính ở trong control plan là `API Server`, `Etcd`,` Controller Manager`, `Scheduler`. Với `Etcd` được dùng làm storage để lưu trữ state, `API Server` là component trung tâm để giao tiếp với các thành phần khác, hai thành phần thực hiện công việc container orchestration là `Controller Manager` và `Scheduler`.Kubernetes được thiết khá phức tạp và cần học nhiều mới có thể sử dụng được. Công việc cài đặt và triển khai một kubernetes cluster cũng không hề đơn giản.|`Nomad` đơn giản hơn khá nhiều, nó chỉ là một binary execute file, và không cần một external services nào cho việc storage cả. Không giống như `Kubernetes`, `Nomad` chỉ tập trung vào việc manager và scheduler workload.
OS Support|`Kubernetes` tập trung hỗ trợ cho Linux containers, các OS khác như MAC và WINDOWS thì chỉ phù hợp cho môi trường dev.|`Nomad` thì không đặc biệt tập trung vào hỗ trợ Linux, các OS khác `Nomad` đều support tốt. `Nomad` support cho cả virtualized, containerized, standalone applications, bao gồm cả Docker, Java, IIS on Windows, Qemu, ...
Deployment|Đối với `Kubernetes`, ta không thể xài cùng một công cụ để cài đặt cho môi trường local Dev và môi trường Production được. Ví dụ, thông thường khi ta triển khai môi trường Local Dev ta sẽ thường dùng minikube, k3s, ... Còn khi cài môi trường Production, ta sẽ xài kubeadm, kops, rancher, ...|`Nomad` chỉ là một binary execute file, nên ta có thể xài cùng một công cụ để cài đặt cho cả môi trường local Dev và môi trường Production.
Scalability|Scale by Node = 5.000 and Scale by Containers = 300.000|Scale by Node = 10.000 and Scale by Containers = 2.000.000

Nếu bạn biết tới `Hashicorp` thì nó có một công cụ là `Terrafrom`, `Terrafrom` sử dụng infrastructure-as-code (IaC) để mô tả và quản lý hạ tầng. `Nomad` cũng tương tự như vậy, nó cho phép ta dùng infrastructure-as-code (IaC) để mô tả và quản lý application. `Nomad` hỗ trợ trên cả ba hệ điều hành là macOS, Windows, và Linux.

Hiện này `Nomad` được sử dụng bởi rất nhiều công ty lớn, điển hình là PagerDuty, Target, Citadel, Trivago, SAP, Pandora, Roblox, eBay, Deluxe Entertainment.

# Why nomad can't replace kubernetes

Tuy `Nomad` hỗ trợ việc scale tốt hơn và dễ học hơn, nhưng `Nomad` chỉ hỗ trợ về manager và scheduler workload.

Các vấn đề như service discovery, secret manager, không có hỗ trợ. Để xây dựng full solution thì phải sử dụng thêm hai tools khác của `Hashicorp` nữa là `Consul` (Service Mesh), `Vault` (Secret Manager).

Với `Nomad` thì để xây dựng được một môi trường full solution thì sẽ xài cả 4 tools nhà `Hashicorp` là `Terrafrom` + `Nomad` + `Consul` + `Vault`.

Cần phải học thêm 2 tools nữa là `Consul` + `Vault`, không đơn giản xíu nào 🤣, tuy nhiên nếu làm chủ được cả 4 tools thì quá tuyệt vời.

![[hashicorp-nomad-3.png]](https://github.com/phucbone/vault/blob/master/imgs/imgs-hashicorp-stack/imgs-hashicorp-nomad/hashicorp-nomad-3.png?raw=true)

Trong khi `Kubernetes` hỗ trợ một môi trường full solution sẵn, manager và scheduler workload thì chắc chắn là có trong `Kubernetes`, vấn đề service discovery thì `Kubernetes` hỗ trợ thông qua CoreDNS, Secret Manager thì nó có Secret resource, chỉ cần học một `Kubernetes` thì có thể control được hầu hết toàn bộ mọi công việc, so với việc phải học cả 3 tools `Nomad` + `Consul` + `Vault`.
