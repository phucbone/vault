# Giới thiệu

Khi triển khai ứng dụng, có thể sử dụng `Deployment`, `StatefulSet` hay `DaemonSet` thì khi đó bạn sẽ đều có một bộ các Pod chạy một ứng dụng.

Các Pod này có vai trò như nhau và đều được cấp 1 IP dùng trong Kubernetes.

Nghĩa là IP này sẽ chỉ được truy cập từ trong Kubernetes. Tuy nhiên các Pod này có thể được tạo hoặc xóa một cách biến động.

Vậy làm sao để chúng ta có thể sử dụng được dịch vụ từ bên ngoài Kubernetes cũng như xử lý câu chuyện Pod bị thay đổi IP liên tục? Câu trả lời là sử dụng service, sẽ giúp chúng ta giải quyết các vấn đề sau:

Expose dịch vụ ra bên ngoài Kubernetes, Client có thể kết nối trực tiếp tới service qua NodePort hoặc qua cấu hình Ingress.

Service sẽ đóng vai trò LoadBalancer cho các Pod ứng dụng mà service quản lý.

Khi các Pod có thay đổi (thêm/bớt hoặc Pod bị xóa và tạo lại) thì ở phía client không cần quan tâm tới IP của Pod.

Nó chỉ cần quan tới tới duy nhất đối tượng Service.

Service là một cách định nghĩa một bộ các Pod và cách thức truy cập vào Pod, thường thông qua cấu hình selector của Service.

Nó giống như cách ReplicaSet quản lý Pod của nó

Thành phần của service

Service là một tài nguyên của Kubernetes và giống với các tài nguyên khác như Pod hay Deployment, ... nó đều có 4 thành phần chính là `apiVersion`, `kind`, `metadata` và `spec`.

Ví dụ Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

Với khai báo trên, ta đã định nghĩa một service có tên my-service, và nó sẽ chọn tới Port TCP 9376 trên tất cả những Pod nào trong cùng Namespace của service mà có nhãn là "app.kubernetes.io/name=MyApp".

Kubernetes sẽ cấp cho đối tượng service này một IP và sẽ được dùng bởi Service Proxies.

Endpoint
Ta đã hiểu rằng Server sẽ scan các Pod thõa mãn các nhãn trong cấu hình selector của nó. Vậy khi các Pod có thay đổi thì Service sẽ cập nhật thông tin đó như thế nào?

Lúc này ta sẽ cần biết tới khái niệm mới là EndPoint. Với mỗi Pod mà Service scan được và thỏa mãn điều kiện select của nó, thì Kubernetes sẽ cập nhật đối tượng Endpoint có tên giống với Service và nó sẽ chứa danh sách các IP:Port của các Pod thỏa mãn điều kiện selector của Service

Ví dụ:

```bash
[k8s@demo]$ kubectl -n demo get service
NAME            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
myapp-service   LoadBalancer   10.233.27.165   <pending>     5000:31123/TCP   19h
```

```bash
[k8s@demo]$ kubectl -n demo get endpoints
NAME            ENDPOINTS                                                           AGE
myapp-service   10.233.67.93:8080,10.233.67.94:8080,10.233.67.95:8080 + 7 more...   19h
```

Service không dùng selector

Service thông thường giúp việc kết nối tới Pod thông qua Selector.

Nhưng khi không sử dụng Selector, thì service có thể giúp khai báo việc kết nối tới nhiều loại tài nguyên hơn ngoài Pod, thậm chí cả các ứng dụng bên ngoài k8s.

Một số usecase có thể sử dụng:

Khai báo Service để kết nối tới một external Data (ngoài Kubernetes)

Trỏ service tới một service khác không dùng namespace hoặc thậm chí trên một cluster khác

Ví dụ

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service      # Tên Service
spec:
  ports:
    - protocol: TCP     # Giao thức
      port: 80          # Port của Service
      targetPort: 9376  # Port target vào Pod
```

Vì Service này không có định nghĩa Selector, đồng nghĩa với việc nó sẽ không có Pod nào để forward tới hay nó sẽ không có EndPoint nào để kết nối tới cả.

Lúc này ta sẽ phải tạo EndPoint cho Service một cách thủ công

Ví dụ:

```yaml
apiVersion: v1
kind: Endpoints
metadata:
  # the name here should match the name of the Service
  name: my-service      # Tên Service
subsets:
  - addresses:
      - ip: 192.0.2.42  # IP Endpoint
    ports:
      - port: 9376      # Port của Service
```

Lưu ý khi tạo EndPoint thủ công cho Service nào thì tên EndPoint phải trùng với Service ấy.

Theo ví dụ trên khi kết nối tới Service my-service port 9376 thì kết nối sẽ được chuyển tới địa chỉ là 192.0.2.42:9376

Multi-Port Service

Đôi khi cần tạo Service để Expose nhiều hơn 1 port.

Kubernetes hỗ trợ khai báo nhiều cấu hình Port trong Service.

Ví dụ

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service                  # Tên Service
spec:
  selector:                         # Match với labels của Pod
    app.kubernetes.io/name: MyApp
  ports:
    - name: http                    # Tên Port: http
      protocol: TCP                 # Loại giao thức: TCP
      port: 80                      # Port 80 của Service my-service với Tên Port là http
      targetPort: 9376              # Port chỉ định của Pod
    - name: https                   # Tên Port: https
      protocol: TCP                 # Loại giao thức: TCP
      port: 443                     # Port 443 của Service my-service với Tên Port là https
      targetPort: 9377              # Port chỉ định của Pod
```

Các loại Service (Service Types)

No|Sevice Type
---|---
1|[[ClusterIP]](https://github.com/phucbone/vault/tree/master/kubernetes/Service/ClusterIP.md)
2|[[NodePort]](https://github.com/phucbone/vault/tree/master/kubernetes/Service/NodePort.md)
3|[[LoadBalancer]](https://github.com/phucbone/vault/tree/master/kubernetes/Service/LoadBalancer.md)
4|[[ExternalName]](https://github.com/phucbone/vault/tree/master/kubernetes/Service/ExternalName.md)
