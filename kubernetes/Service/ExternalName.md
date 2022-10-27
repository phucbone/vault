# Giới thiệu

`ExternalName` là một trường hợp đặc biệt, nó không sử dụng selector mà thay vào đó sử dụng một DNS name.

`ExternalName` có thể ứng dụng để khai báo kết nối tới các dịch vụ bên ngoài Kubernetes.

Ví dụ

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-database-svc                     # Tên Service
  namespace: prod                           # Namespace
spec:
  type: ExternalName                        # Loại Service: ExternalName
  externalName: my.database.example.com     # externalName
```

Khi kết nối tới `my-database-svc` thì `DNS` của `Kubernetes` sẽ trả về một bản ghi để kết nối tới `my.database.example.com`
