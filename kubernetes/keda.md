![[keda-presend.png]]

---
# Giới thiệu

>KEDA - Kubernetes-based Event Driven Autoscaler

`KEDA` là một resource giúp ta trong việc scale các pod container trong `Kubernetes` một cách rất dễ dàng và cover rất nhiều use case phổ biến.

Khi ta sử dụng `Kubernetes`, để scale một pod container ta thường sử dụng [Horizontal Pod Autoscaling](https://viblo.asia/p/kubernetes-series-bai-16-automatic-scaling-pod-va-cluster-YWOZrGyRlQ0).

Nhưng `HPA` chỉ hỗ trợ ta scale container theo một vài metric đơn giản (Ví dụ như là CPU và MEMORY)

Nếu ta muốn scale container theo các metric khác thì ta phải viết custom metrics khá phức tạp (Ví dụ như là scale container dựa theo Kafka topic)

Thay vì phải tự viết thì ta có thể sử dụng `KEDA` để làm công việc này.

`KEDA` là một `Custom Resource Definition` mà ta có thể thêm vào một `Kubernetes Cluster` có sẵn.

`KEDA` sẽ tương tác với `HPA` ở bên dưới để mở rộng thêm function của `HPA`.

Với `KEDA` ta có thể scale một ứng dụng dựa vào event-driven (Ví dụ như số lượng message bên trong `RabbitMQ` đạt tới một số lượng nhất định)

## High-level Architecture

Để hiểu rõ hơn về `KEDA` thì ta sẽ coi sơ qua kiến trúc high-level của nó

![[keda-flow.png]]

KEDA bao gồm các thành phần cơ bản

Part|Description
---|---
`Metrics adapter`|Thành phần hành động tương tự như [kubernetes metrics server](https://github.com/kubernetes-sigs/metrics-server), mà sẽ expose các rich event data như queue length hoặc stream lag tới `Horizontal Pod Autoscaler` để xử lý việc scale
`Controller`|Thành phần thực hiện việc scale container về 0
`Scaler`|Đóng vai trò là một connector để kết nối tới external event source như `Kafka`, `RabbitMQ` và thu thập metric về
`ScaledObject`|Định nghĩa quan hệ giữa event source và workload resource (`Deployment`, `StatefulSet`)

## Event sources.

`KEDA` hỗ trợ rất nhiều event sources, hỗ trợ với cả các resource của `AWS` và `AZURE`

![[keda-event-sources.png]]

>Đọc thêm: https://keda.sh/docs/2.6/concepts/#event-sources-and-scalers

---
# Cài đặt

Dùng `Helm` để cài `KEDA`

```bash
helm repo add kedacore https://kedacore.github.io/charts
helm repo update
kubectl create namespace keda
helm install keda kedacore/keda --namespace keda
```

---
# Testing: Scale with RabbitMQ

Ta sẽ tạo `RabbitMQ` + `Consumer`, sau đó tạo một `ScaledObject` mà chỉ định thông số để consumer scale theo số lượng message.

## Tạo RabbitMQ

`rabbitmq.yaml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rabbitmq-cm
  labels:
    component: rabbitmq
data:
  rabbitmq.conf: |
    default_user = k8s-practice
    default_pass = k8s-practice

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rabbitmq-deployment
  replicas: 1
  strategy:
    type: Recreate
  selector:
    matchLabels:
      component: rabbitmq
  template:
    metadata:
      labels:
        component: rabbitmq
    spec:
      containers:
        - image: rabbitmq
          name: rabbitmq
          ports:
            - containerPort: 5672
          env:
            - name: RABBITMQ_NODENAME
              value: k8s-practice
          volumeMounts:
            - name: config
              mountPath: /etc/rabbitmq
      volumes:
        - name: config
          configMap:
            name: rabbitmq-cm

---
apiVersion: v1
kind: Service
metadata:
  name: rabbitmq-service
spec:
  selector:
    component: rabbitmq
  ports:
    - port: 5672
```

```bash
kubectl apply -f rabbitmq.yaml
```

## Tạo Comsumer

`share-comsume-queue.yaml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: share-consume-queue-cm
data:
  RABBITMQ_HOST: rabbitmq
  RABBITMQ_PORT: "5672"
  RABBITMQ_USER: k8s-practice
  RABBITMQ_PASS: k8s-practice

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: share-consume-queue-deployment
  labels:
    component: share-consume-queue
spec:
  selector:
    matchLabels:
      component: share-consume-queue
  template:
    metadata:
      labels:
        component: share-consume-queue
    spec:
      containers:
        - name: share-consume-queue
          image: 080196/k8s-practice-keda-consumer
          envFrom:
            - configMapRef:
                name: share-consume-queue-cm

```

```bash
kubectl apply -f share-comsume-queue.yaml
```

## Tạo ScaledObject

`scaled-object.yaml`

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: rabbitmq-secret
data:
  MQ_HOST: YW1xcDovL2s4cy1wcmFjdGljZTprOHMtcHJhY3RpY2VAcmFiYml0bXEuZGVmYXVsdDo1NjcyL3Zob3N0 # base64 encoded value of format amqp://k8s-practice:k8s-practice@rabbitmq.default:5672/vhost

---
apiVersion: keda.sh/v1alpha1
kind: TriggerAuthentication
metadata:
  name: rabbitmq-trigger-auth
spec:
  secretTargetRef:
    - parameter: host
      name: rabbitmq-secret # secret name
      key: MQ_HOST # secret key

---
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: share-consume-queue
spec:
  scaleTargetRef:
    name: share-consume-queue
  triggers:
    - type: rabbitmq
      metadata:
        mode: QueueLength
        queueName: scale_out
        value: "5"
      authenticationRef:
        name: rabbitmq-trigger-auth

```

```bash
kubectl apply -f scaled-object.yaml
```

>Đọc thêm: https://keda.sh/docs/2.6/scalers/rabbitmq-queue

### Giải thích

#### Secret

Đầu tiên là `Secret` dùng để lưu connection tới `RabbitMQ`

Giá trị `MQ_HOST` là chuỗi base64 của connection string `amqp://k8s-practice:k8s-practice@rabbitmq.default:5672/vhost`, ta tạo ra nó bằng câu lệnh encode

```bash
echo -n "amqp://k8s-practice:k8s-practice@rabbitmq.default:5672/vhost" | base64 -w 0
```

#### TriggerAuthentication

Thứ hai là `TriggerAuthentication` resource, được `Scaler` dùng để tạo connection tới `RabbitMQ`

Giá trị của `secretTargetRef` sẽ gồm 3 thuộc tính
- `parameter`
- `name` (tên của `Secret`)
- `key` (giá trị `key` trong trường data của `Secret`)

#### ScaledObject

Cuối cùng là `ScaledObject`, thành phần chính để config scale cho consumer.

Với giá trị của `scaleTargetRef` là resource ta chọn để scale

Giá trị `triggers` dùng để định nghĩa event mà ta dùng để thực hiện scale

```yaml
- type: rabbitmq
  metadata:
    mode: QueueLength
    queueName: scale_out
    value: "5"
    vhostName: /
  authenticationRef:
    name: rabbitmq-trigger-auth
```

Thuộc tính `type` ta chọn là `rabbitmq`

`metadata`:
- `mode` ta chỉ định giá trị là `QueueLength`
- `queueName` là tên của queue ta theo dõi message của nó
- `value` là số lượng message tương ứng với một replica, số lượng replica sẽ được tính dựa theo số này (Ví dụ số lượng message là 30 thì ta sẽ scale lên 6 replica)

Giá trị của trường `authenticationRef` là tên của `TriggerAuthentication`.

Sau khi tạo `ScaledObject` xong, lúc này kiểm tra lại `Deployment` thì sẽ thấy `share-consume-queue` của đã scale xuống còn `0` replica.

>Một điểm mà `KEDA` khác với HPA` `bình thường là cho phép scale một resource xuống còn `0` replica

## Tạo Job

Tạo Job để nó push message vào queue

Sau đó kiểm tra consumer có scale như đã nói hay không

`publisher-job.yaml`

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: rabbitmq-publish
spec:
  backoffLimit: 4
  template:
    spec:
      restartPolicy: Never
      containers:
      - name: rabbitmq-client
        image: 080196/k8s-practice-keda-send
        env:
          - name: QUEUE_LENGTH
            value: "50"
        envFrom:
          - configMapRef:
              name: share-consume-queue
```

```bash
kubectl apply -f publisher-job.yaml
```

Ở trên ta send 50 message vào queue. 

Sau đó get pod lại, lúc này sẽ thấy số lượng pod đã tăng lên.

```bash
kubectl get deploy,pod,hpa
```

Lúc này thì pod mới scale lên 4 pod, đợi một lúc get lại `Deployment` thì sẽ thấy nó đã scale lên 10 replica

```bash
kubectl get deploy,pod,hpa
```

Đợi một thời gian khi consumer tiêu thụ hết message trong queue, lúc này pod sẽ scale xuống lại

```bash
kubectl get deploy,pod,hpa
```
