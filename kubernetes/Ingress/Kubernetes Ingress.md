# Ingress là gì?

`Ingress` hiển thị các tuyến HTTP và HTTPS từ bên ngoài cluster đến các services trong cluster.

Định tuyến lưu lượng được kiểm soát bởi các quy tắc được xác định trên tài nguyên `Ingress`.

Dưới đây là một ví dụ đơn giản trong đó `Ingress` gửi tất cả lưu lượng truy cập của mình đến một Service:

```mermaid
flowchart LR
	id1([client])
	subgraph cluster
		Ingress
		Service
		id2[Pod]
		id3[Pod]
	end
	id1 -->|Ingress-managed load balancer|Ingress
	Ingress -->|routing rule|Service
	Service --> id2
	Service --> id3
```

`Ingress` có thể được định cấu hình để cung cấp cho Service các URL có thể truy cập bên ngoài, lưu lượng cân bằng tải, chấm dứt SSL / TLS và cung cấp dịch vụ lưu trữ ảo dựa trên tên.

Bộ điều khiển `Ingress` chịu trách nhiệm thực hiện `Ingress`, thường là với bộ cân bằng tải, mặc dù nó cũng có thể định cấu hình bộ định tuyến biên của bạn hoặc các giao diện người dùng bổ sung để giúp xử lý lưu lượng.

`Ingress` không hiển thị các cổng hoặc giao thức tùy ý. 

Việc hiển thị các services không phải HTTP và HTTPS với internet thường sử dụng 1 trong 2 loại service: `Service.Type=NodePort` hoặc `Service.Type=LoadBalancer`

## Ingress Controller

No|Technology
---|---
1|nginx
2|contour
3|haproxy
4|treafik
5|istio
6|

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-configuration
```

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-ingress-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      name: nginx-ingress
  template:
    metadata:
      labels:
        name: nginx-ingress
    spec:
      containers:
        - name: nginx-ingress-controller
          image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.21.0
      args:
        - /nginx-ingress-controller
        - --configmap=$(POD_NAMESPACE)/nginx-configuration
      env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
      ports:
        - name: http
          containerPort: 80
        - name: https
          containerPort: 443
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-ingress
spec:
  type: NodePort
  selector:
    name: nginx-ingress
  ports:
    - name: http
      port: 80
      targetPort: 80
      protocol: TCP
    - name: https
      port: 443
      targetPort: 443
      protocol: TCP
```

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nginx-ingress-serviceaccount
```


## Ingress Resources

### Rule 1

```mermaid
flowchart LR
	id10([www.demo.com])
	nginx
	id11((/wear))
	id10 --> nginx
	nginx --> id11
```


```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-demo
spec:
  rules:
    - http:
        paths:
          - path: "/wear"
            pathType: Prefix
            backend:
              service:
                name: wear-service
                port:
                  number: 80
```

### Rule 2

```mermaid
flowchart LR
	id20([www.demo.com])
	nginx
	id21((/wear))
	id22((/watch))
	id23((/listen))
	id20 --> nginx
	nginx --> id21
	nginx --> id22
	nginx --> id23
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-demo
spec:
  rules:
    - http:
        paths:
          - path: /wear
            pathType: Prefix
            backend:
              service:
                name: wear-service
                port:
                  number: 80
          - path: /watch
            pathType: Prefix
            backend:
              service:
                name: watch-service
                port:
                  number: 80
          - path: /listen
            pathType: Prefix
            backend:
              service:
                name: listen-service
                port:
                  number: 80
```

### Rule 3

```mermaid
flowchart LR
	id30([wear.demo.com])
	id31([watch.demo.com])
	nginx
	id32((wear))
	id33((watch))
	id34((/returns))
	id35((/support))
	id36((/movies))
	id37((/tv))
	id30 --> nginx
	id31 --> nginx
	nginx --> id32
	nginx --> id33
	id32 --> id34
	id32 --> id35
	id33 --> id36
	id33 --> id37
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-demo
spec:
  rules:
    - host: wear.demo.com
      http:
        paths:
          - path: "/returns"
            pathType: Prefix
            backend:
              service:
                name: returns-service
                port:
                  number: 80
          - path: "/support"
            pathType: Prefix
            backend:
              service:
                name: support-service
                port:
                  number: 80
    - host: watch.demo.com
      http:
        paths:
          - path: "/movies"
            pathType: Prefix
            backend:
              service:
                name: returns-service
                port:
                  number: 80
          - path: "/tv"
            pathType: Prefix
            backend:
              service:
                name: support-service
                port:
                  number: 80
```

### Rule 4: Simple fanout

```mermaid
flowchart LR
	id1([client])
	subgraph cluster
		id2[Ingress, 178.91.123.132]
		id3[Service service1:4200]
		id4[Service service2:8080]
		id5[Pod]
		id6[Pod]
		id7[Pod]
		id8[Pod]
	end
	id1 -->|Ingress-managed load balancer|id2
	id2 -->|/foo|id3
	id2 -->|/bar|id4
	id3 --> id5
	id3 --> id6
	id4 --> id7
	id4 --> id8
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-demo
spec:
  rules:
    - host: foo.bar.com
      http:
        paths:
          - path: "/foo"
            pathType: Prefix
            backend:
              service:
                name: service1
                port:
                  number: 4200
          - path: "/bar"
            pathType: Prefix
            backend:
              service:
                name: service2
                port:
                  number: 8080
```

### Rule 5: Ingress Name Based Virtual hosting

```mermaid
flowchart LR
	id50([client])
	subgraph cluster
		id51[Ingress, 178.91.123.132]
		id52[Service service1:80]
		id53[Service service2:80]
		id54[Pod]
		id55[Pod]
		id56[Pod]
		id57[Pod]
	end
	id50 -->|Ingress-managed load balancer|id51
	id51 -->|Host: foo.bar.com|id52
	id51 -->|Host: bar.foo.com|id53
	id52 --> id54
	id52 --> id55
	id53 --> id56
	id53 --> id57
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-demo
spec:
  rules:
    - host: foo.bar.com
      http:
        paths:
          - path: "/"
            pathType: Prefix
            backend:
              service:
                name: service1
                port:
                  number: 80
    - host: bar.foo.com
      http:
        paths:
          - path: "/"
            pathType: Prefix
            backend:
              service:
                name: service2
                port:
                  number: 80
```

### Rule 6: With TLS

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: testsecret-tls
  namespace: default
type: kubernetes.io/tls
data:
  tls.crt: base64 encoded cert
  tls.key: base64 encoded key
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-demo
spec:
  rules:
    - hosts:
        - https-example.com
      secretName: testsecret-tls
  rules:
    - host: https-example.com
      http:
        paths:
          - path: "/"
            pathType: Prefix
            backend:
              service:
                name: service1
                port:
                  number: 80
```

