# Giới thiệu

`ClusterIP` service là loại `Service` mặc định trong `Kubernetes`.

Khi tạo `Service` dạng này, nó sẽ được cấp 1 IP và sẽ chỉ sử dụng được từ bên trong của `Kubernetes`.

`ClusterIP` thường được dùng cho các `Service` mà chỉ dùng cho nội bộ `Kubernetes`.

Muốn `Expose` những `Service` này ra bên ngoài `Kubernetes` thì có thể sử dụng `Ingress` (https://github.com/phucbone/vault/tree/master/kubernetes/Ingress/ingress.md)

# Demo

Tạo demo đơn giản với manifest file `demo-clusterip.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app                  # Tên Pod
  namespace: demo               # Namespace triển khai
  labels:                       # Các Labels của Pod
    app: my-app
    apptype: frontend
spec:
  containers:
  - name: nginx-container       # Tên Container
    image: nginx                # Image của Container
    ports:
    - containerPort:  80        # Port của Container
      name:  http               # Tên của Port Container

---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service        # Tên Service
  namespace: demo               # Namespace triển khai
spec: 
  type: ClusterIP               # Loại Service: ClusterIP
  selector:                     # Khai báo rule để lọc các Pod mà Service sẽ forward connection tới
    app: my-app
    apptype: frontend
  ports:
    - targetPort: 80            # Port của Pod, Service sẽ chuyển tiếp kết nối vào đây
      port: 80                  # Port được mở của Service. Client sẽ kết nối với Service qua port này
```

```bash
kubectl create namespace demo
kubectl apply -f demo-clusterip.yaml
```

Lúc này `Service` có tên `frontend-service` đã được sinh ra có thông tin như sau

```bash
[k8s@demo]$ kubectl -n demo get service -o wide
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE     SELECTOR
frontend-service   ClusterIP      10.233.2.148    <none>        80/TCP           5m27s   app=my-app,apptype=frontend
```

Ta có thể kết nối từ trong `Kubernetes` (từ bất cứ `Node` nào trong `Kubernetes`) tới IP của `Service` nhưng không thể kết nối từ bên ngoài được.

Ta sẽ đứng từ một `Master Node` để kết nối tới `Service`:

```bash
[k8s@demo]$ kubectl -n demo get service
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
frontend-service   ClusterIP      10.233.2.148    <none>        80/TCP           6m56s
```

```bash
[k8s@demo]$ curl 10.233.2.148:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```