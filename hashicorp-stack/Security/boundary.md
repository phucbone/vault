![[hashicorp-boundary-presend.jpg]](https://github.com/phucbone/vault/blob/master/imgs/imgs-hashicorp-stack/imgs-hashicorp-boundary/hashicorp-boundary-presend.jpg?raw=true)

# Giới thiệu

`Boundary` là công cụ tiếp theo ta cần học trong bộ công cụ Hashicorp Stack. 

`Boundary` được sử dụng để quản lý truy cập vào hệ thống, nó là một thằng khá hay và hữu ích, giúp công việc của DevOps giảm đi rất nhiều.  
  
Ví dụ:

Ta có một con Database nằm ở private network và để truy cập được nó từ bên ngoài thì ta sẽ tạo một con bastion host, việc quản lý ai có thể truy cập được con bastion host này cũng khá lằng nhằng.  
  
Nên thông thường ta chỉ tạo một SSH key rồi vứt cho toàn bộ những ai cần truy cập, việc này không hề bảo mật nhưng do tiện với nhanh nên rất nhiều người sử dụng cách này.

Bạn nào kĩ hơn thì sẽ tạo và quản lý user trên linux, nhưng nếu ta có thêm một con bastion host thì ta lại phải tạo user từ đầu. 

Do đó `Boundary` đã được ra đời và nó sẽ giúp ta khi gặp vấn đề thế này, nó sẽ giúp ta tạo user ở một nơi và quản lý truy cập cho toàn bộ hệ thống.
