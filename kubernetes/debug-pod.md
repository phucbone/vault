# Giới thiệu

Thông thường để `debug` một `container` ở trên `Kubernetes`, thường sẽ dùng câu lệnh `kubectl logs` để xem logs lỗi hoặc dùng câu lệnh `kubectl exec` để có thể chạy câu lệnh `commands` ở bên trong `container`.

Nhưng nếu gặp trường hợp `container` chạy không có hỗ trợ `debugging utilities` như `shell`, `curl`, `bash` hoặc `container` của ta ở trạng thái `CrashLoopBackOff` thì sẽ `debug` như thế nào?

Để giải quyết vấn đề trên thì `Kubernetes` có cung cấp một feature đó là `ephemeral debug container` (Vùng chứa gỡ lỗi tạm thời)

>Đây là tính năng được giới thiệu ở `Kubernetes v1.16` và ở version mới nhất là `v1.23` thì nó đã chuyển lên bản beta.

![[debug-pod-flow.png]](https://github.com/phucbone/vault/blob/master/imgs/imgs-kubernetes/imgs-debug-pod/debug-pod-flow.png)

---
# Debug Running Pods

## Container logs

Cách đầu tiên và thông dụng nhất là sử dụng câu lệnh `kubectl logs`

```bash
kubectl logs ${POD_NAME} ${CONTAINER_NAME}
```

Nó sẽ liệt kê toàn bộ logs của container đó ra, ta xem logs để debug. 

Nếu muốn xem logs của previous crashed container, thêm `--previous` vào nữa.

```bash
kubectl logs --previous ${POD_NAME} ${CONTAINER_NAME}
```

## Container exec

Cách thứ hai là dùng câu lệnh `kubectl exec`

```bash
kubectl exec ${POD_NAME} -c ${CONTAINER_NAME} -- ${CMD} ${ARG1} ${ARG2} ... ${ARGN}
```

Nó sẽ cho phép thực thi câu lệnh command lên trên container của pod đang chạy.

Ví dụ

```bash
kubectl exec -it ${POD_NAME} -- node -v
```

Hoặc

```bash
kubectl exec -it ${POD_NAME} -- sh
```


---
# Debugging with an ephemeral debug container

Nếu gặp trường hợp `container` chạy không có hỗ trợ `debugging utilities` như `shell`, `curl`, `bash` hoặc `Pod` ở trạng thái `CrashLoopBackOff` thì không thể dùng `kubectl exec` được, vì sẽ có thông báo lỗi

>OCI runtime exec failed: exec failed: container_linux.go:346: starting container process caused "exec: \"sh\": executable file not found in $PATH": unknown

Để giải quyết vấn đề này và để việc debug dễ dàng hơn, `Kubernetes` cho ra một tính năng là `ephemeral debug container`.

Chạy `kubectl debug` để tạo một `ephemeral container` giúp debug container chính. 

Ví dụ có một container sau và nó sẽ không có hỗ trợ `debugging utilities` như `shell`, `bash`

```bash
kubectl run pod-not-debug-tool --image=k8s.gcr.io/pause:3.1 --restart=Never
```

Sử dụng `ephemeral container` để debug

```bash
kubectl debug pod-not-debug-tool --image=busybox --target=pod-not-debug-tool
```

Giải thích
- Giá trị sau `debug` sẽ là tên của container
- `--target` là tên của pod
- `--image` là tên image sử dụng

Sau khi chạy câu lệnh trên thì terminal của ta sẽ hiển thị command promp của debug container, ta có thể dùng nó để debug container chính.

>Defaulting debug container name to debugger-8xzrl.
>If you don't see a command prompt, try pressing enter.
>/ #

> Lưu ý: Khi khởi tạo `Kubernetes Cluster` phải bật lên thì mới sử dụng được tính năng này.
> Ví dụ `Kubernetes Cluster` được tạo bằng `kubeadm` thì phải thêm option `--feature-gates=EphemeralContainers=true`. Và option `--target` phải được support bởi `Container Runtime`.

## Sao chép Pod trong khi thay đổi command của nó

Để debug container pod mà ở trạng `CrashLoopBackOff`

Trong một số trường hợp container trong pod không thể chạy được, bị crash trước khi khởi động

Ví dụ

```bash
kubectl run pod-crashed --image=busybox -- false
```

Kiểm tra thì sẽ thấy bị `CrashLoopBackOff`

```bash
kubectl describe pod pod-crashed
```

>Containers:
  myapp:
    Image:         busybox
    ...
    Args:
      false
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Error
      Exit Code:    1

Debug với lệnh

```bash
kubectl debug pod-crashed -it --copy-to=pod-crashed-debug --container=container-crashed-debug -- sh
```

>If you don't see a command prompt, try pressing enter.
>/ #

Cậu lệnh trên sẽ copy container pod-crashed vào trong container container-crashed-debug của pod-crashed-debug và chạy lệnh `sh` ở trong container-crashed-debug để debug pod.

## Sao chép Pod trong khi thay đổi container images

Sẽ có một vài trường hợp là container chạy không đủ công cụ giúp ta debug một container crashed, thì có thể copy container sang một image container khác mà có nhiều debug tool hơn. 

Ví dụ

```bash
kubectl run pod-crashed-two --image=busybox --restart=Never -- sleep 1d
```

Trong `busybox` sẽ không có tool `nslookup` để ta debug dns server, ta có thể copy nó sang một image khác mà có sẵn `nslookup` tool như là image ubuntu.

```bash
kubectl debug pod-crashed-two --copy-to=pod-crashed-debug-two --set-image=*=ubuntu
```

Cú pháp `--set-image` sẽ thay đổi `image` của toàn bộ `container` trong `pod` đó thành `ubuntu`.