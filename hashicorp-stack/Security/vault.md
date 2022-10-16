![[hashicorp-vault-presend.jpg]](https://github.com/phucbone/vault/blob/master/imgs/imgs-hashicorp-stack/imgs-hashicorp-vault/hashicorp-vault-presend.jpg?raw=true)

# Giới thiệu

`Vault` là công cụ tiếp theo ta cần học trong bộ công cụ `Hashicorp Stack`. 

`Vault` được sử dụng để quản lý dữ liệu mật, những công cụ này thường được gọi là `Secret Manager`, đây là mảng còn mới lạ ở Việt Nam nhưng nếu bạn nắm bắt được xu thế thì bạn sẽ thành những người đi trước.  
  
`Vault` rất dễ dàng sử dụng và hỗ trợ chúnng ta giải quyết các vấn đề rất nhức đầu về bảo mật dữ liệu

Ví dụ:

Trong `Kubernetes` thì `Secret` của nó chỉ lưu được dữ liệu tối đa là 1MiB, nếu ta cần `Secret` mà lớn hơn 1MiB thì bó tay, do đó ta cần phải sử dụng các công cụ khác, `Vault` là lựa chọn khá tốt trong trường hợp này.

# Cài đặt

```bash
helm repo add hashicorp https://helm.releases.hashicorp.com
helm search repo hashicorp/vault
```
