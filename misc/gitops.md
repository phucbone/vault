# Giới thiệu

`GitOps` là tập họp của những công cụ có nhiệm vụ đồng hộ trạng thái của hệ thống hiện tại cho giống với trạng thái của hệ thống được cấu hình trong `git repository`.

Nếu muốn thay đổi cấu hình hệ thống --> phải thực hiện việc đó thông qua việc sửa đổi các file cấu hình trong source code --> sau đó thực hiện `git commit` và `git merge`.

Và tiếp theo các công cụ `GitOps` sẽ đồng bộ cấu hình vừa thay đổi trong source code lên hệ thống thực tế.

Một công cụ `GitOps` phổ biến là [[argocd]](https://github.com/phucbone/vault/blob/master/cicd/cd/argocd.md).

![[gitops-presend.jpg]](https://github.com/phucbone/vault/blob/master/imgs/imgs-misc/imgs-gitops/gitops-presend.jpg?raw=true)