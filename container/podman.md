![[podman-presend.jpg]](https://github.com/phucbone/vault/blob/master/imgs/imgs-container/podman-presend.jpg)

# Giới thiệu

`Podman` cũng là một công cụ để ta tương tác được với container giống như `Docker`, nó được thiết kế sau `Docker` và có toàn bộ câu command giống như `Docker` để ta tương tác với container. 

Để sử dụng `Podman` ta chỉ đơn giản thêm `alias docker=podman` vào trong `~/.bashrc`.

Tại sao chúng ta lại cần `Podman` trong khi đã có `Docker`?  
  
Nếu bạn cần một công cụ nhỏ gọn với yêu cầu về bảo mật cao, hoặc bạn có ý định sử dụng `Kubernetes` thì `Podman` là lựa chọn tốt hơn cho bạn.  
  
Nếu bạn cần công cụ mà có tài liệu rõ ràng và có cộng đồng lớn thì `Docker` là lựa chọn tốt hơn cho bạn.