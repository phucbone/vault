# Gioi thieu

`NodePort` là một cách đơn giản nhất để có thể truy cập đến `Service` từ bên ngoài `Kubernetes`.

`NodePort` sẽ mở một port cụ thể trên tất cả các `Node` trong `Cluster` và lưu lượng truy cập được gửi đến bất kì `Node` nào trong `Cluster` thông qua port được mở sẽ được chuyển tiếp đến dịch vụ bên trong `Cluster`.

`NodePort` được giới hạn sử dụng trong dải port `3000-32767`.

Có thể khai báo nodePort trong manifest yaml hoặc `Kubernetes` sẽ tự động generate nếu không set nó.

Tạo demo `NodePort` từ manifest file `demo-nodeport.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app             # Tên Pod
  namespace: demo          # Namespace triển khai
  labels:                  # Các Label của Pod
    app: my-app
    apptype: frontend
spec:
  containers:
  - name: nginx-container  # Tên Container
    image: nginx           # Image Container
    ports:
    - containerPort:  80   # Container port
      name:  http          # Tên Port Container

---
apiVersion: v1 
kind: Service 
metadata:
  name: service-nodeport   # Tên Service
  namespace: demo          # Namespace triển khai
spec:   
  type: NodePort           # Loại service: NodePort
  selector:                # Khai báo rule để lọc các Pod mà Service sẽ forward connection tới
    app: my-app
    apptype: frontend
  ports: 
    - targetPort: 80       # Port của Pod, Service sẽ chuyển tiếp kết nối vào đây
      port: 80             # Port được mở của SerServicevice. Client sẽ kết nối với Service qua port
      nodePort: 30888      # Port được mở trên tất cả các Node để nhận request cho Service
```

```bash
kubectl apply -f demo-nodeport.yaml
```

Kiểm tra

```bash
[k8s@demo]$ kubectl -n demo get svc -owide
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE   SELECTOR
frontend-service   ClusterIP      10.233.2.148    <none>        80/TCP           14m   app=my-app,apptype=frontend
service-nodeport   NodePort       10.233.51.68    <none>        80:30888/TCP     5s    app=my-app,apptype=frontend
```

Lúc này có thể truy cập tới Service từ bên ngoài `Kubernetes` bằng `[node-ip]:[node-port]`

```bash
[k8s@demo]$ curl 192.168.10.11:30888
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

NodePort cũng có ClusterIP để kết nối tới các Service khác trong Cluster

```bash
[k8s@demo]$ kubectl -n demo get service
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
frontend-service   ClusterIP      10.233.2.148    <none>        80/TCP           17m
service-nodeport   NodePort       10.233.51.68    <none>        80:30888/TCP     3m7s
```

```bash
[k8s@demo]$ curl 10.233.51.68:80
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
