`LoadBalancer` được sử dụng khi ta muốn có một địa chỉ IP duy nhất sẽ chuyển tiếp tất cả các yêu cầu truy cập đến `Service`.

`LoadBalancer` được sử dụng nhiều trên `Cloud` bởi các `Cloud Provider` đã cung cấp `External Load Balancers`, còn trên `On-Premise` thì mặc định sẽ không có (nếu muốn có phải tự cài đặt riêng).

Khi chạy trên `On-Premise` thì `LoadBalancer` gần như là `NodePort`.

Tạo demo đơn giản với manifest `demo-lb.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app              # Tên của Pod
  namespace: demo           # Namespace triển khai
  labels:                   # Các Label của Pod
    app: my-app
    apptype: frontend
spec:
  containers:
  - name: nginx-container   # Tên Container trong Pod
    image: nginx            # Image Container
    ports:
    - containerPort: 80     # Port của Container
      name: http            # Tên của Port

---
apiVersion: v1
kind: Service
metadata:
  name: service-lb          # Tên Service
  namespace: demo           # Namespace triển khai
spec:
  type: LoadBalancer        # Loại Service: LoadBalancer
  ports:
    - port: 80              # Port của Pod, Service sẽ chuyển tiếp kết nối vào đây
      targetPort: 80        # Port được mở của Service. Client sẽ kết nối với Service qua port
      nodePort: 30999       # Port của Node được Expose ra
  selector:                 # Tương ứng với labels của resource Pod
    app: my-app
    apptype: frontend
```

```bash
kubectl create namespace demo
kubectl apply -f demo-lb.yaml
```

Kiểm tra

```bash
[k8s@demo]$ kubectl -n demo get service
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service-lb         LoadBalancer   10.233.2.184    <pending>     80:30999/TCP     9s
```

Cột `EXTERNAL-IP` của `service-lb` ở trạng thái `pending`, do demo này đang build `On-Premise` nên sẽ không được cấp external loadbalancer.

Tuy nhiên vẫn kết nối được tới `Service` như qua `NodePort`.

Kết nối qua `ClusterIP`

```bash
[k8s@demo]$ curl 10.233.2.184:80
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

Kết nối qua `NodePort`

```bash
[k8s@demo]$ curl 192.168.10.11:30999
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