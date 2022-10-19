# Containerization và Hypervisor

## Hypervisor là gì ?

`Hypervisor` là một công nghệ ảo hóa ở tầng `hardware` (phần cứng), có thể liệt kê ra một vài ứng dụng như là `VMWare, Virtual box, Bluestack`...

Ưu điểm của việc sử dụng `hypervisor` chính là rất dễ sử dụng, chỉ cần cài ứng dụng lên máy và chúng ta sẽ có cafi được nhiều các hệ điều hành khác nhau trên máy rồi. Tuy nhiên có lợi thì ắt phải có hại, mà có vẻ đối với `hypervisor` lại có nhiều hại hơn

-   Lãng phí tài nguyên: khi cài đặt chúng ta cần phải chia sẻ ổ cứng hay RAM từ máy thật sang máy ảo,
-   Thời gian khởi động rất lâu : Hồi còn đi học đại học thì mỗi lần chạy máy ảo (VMware) trên máy thì là một sự cực hình với mình, nó quá lâu =))

Chính vì những nhược điểm trên mà sau này đã cho ra mắt công nghệ `Containerization`.

## Containerization là gì ?

`Containerization` là công nghệ ảo hóa ở tầng OS (hệ điều hành), công nghệ được sử dụng ở đây chính là Docker.

Cũng giống với `Hypervioser` thì `Containerization` cũng cho chúng ta có thể cài được nhiều máy ảo trên máy thật. Sự khác biệt là chúng dúng chung luôn tài nguyên với máy thật mà không phải chia chác gì cả. Dùng bao nhiêu thì cấp bấy nhiêu, dùng xong thì giải phóng.

Công nghệ

No|Technology
---|---
1|[[docker]](https://github.com/phucbone/vault/blob/master/container/docker/docker.md)
2|[[podman]](https://github.com/phucbone/vault/blob/master/container/podman/podman.md)