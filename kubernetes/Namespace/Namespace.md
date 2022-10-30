# Giới thiệu

Trong `Kubernetes`, `Namespace` là cơ chế để chúng ta có thể tách biệt các nhóm tài nguyên (như `Pod`, `Deployment` hay `Service` ...) trong cùng một cluster.

Tên của các tài nguyên (resources) này là duy nhất trong `Namespace` đó, nhưng có thể được sử dụng ở một `Namespace` khác.

Ví dụ

Nếu ta đã tạo `Pod` tên là `nginx1` trong `Namespace` `demo1` thì ta không thể tạo thêm `Pod`  tên `nginx1` trong `Namespace` `demo1` này nữa vì đã tồn tại rồi.

Tuy nhiên ta vẫn có thể tạo `Pod` `nginx1` trong `Namespace` `demo2` mà không sợ trùng với `Pod` `nginx1` ở `Namespace` `demo1` vì chúng ở 2 `Namespace` khác nhau.

Các `Namespace` khác nhau được phân biệt bởi tên `Namespace`.

`Namespace` có cấu trúc phẳng, tức là ta không thể tạo `Namespace` bên trong `Namespace` được.

Tài nguyên được chia làm 2 loại

Resource Level| Description
---|---
`Namespace`|Tức là thuộc quản lý của 1 Namespace nào đó. Ví dụ Pods, Deployments, Services, ...
`Cluster`|Không thuộc một Namespace cụ thể nào mà thuộc quản lý của Cluster như Nodes, StorageClass, Persistent Volumes...

Namespace và thành phần bên trong



---
# Phân biệt tài nguyên mức Namespace và mức Cluster

Liệt kê các loại tài nguyên

```bash
kubectl api-resources
```

Tài nguyên mức `Namespace` có giá trị ở cột "`NAMESPACED`" bằng `true`.

```bash
[k8s@demo]$ kubectl api-resources -o wide --namespaced=true
---
NAME                        SHORTNAMES   APIVERSION              NAMESPACED   KIND                       VERBS
configmaps                  cm           v1                      true         ConfigMap                  [create delete deletecollection get list patch update watch]
endpoints                   ep           v1                      true         Endpoints                  [create delete deletecollection get list patch update watch]
pods                        po           v1                      true         Pod                        [create delete deletecollection get list patch update watch]
replicationcontrollers      rc           v1                      true         ReplicationController      [create delete deletecollection get list patch update watch]
serviceaccounts             sa           v1                      true         ServiceAccount             [create delete deletecollection get list patch update watch]
services                    svc          v1                      true         Service                    [create delete get list patch update watch]
deployments                 deploy       apps/v1                 true         Deployment                 [create delete deletecollection get list patch update watch]
replicasets                 rs           apps/v1                 true         ReplicaSet                 [create delete deletecollection get list patch update watch]
<output truncated>
```

Tài nguyên mức `Cluster` giá trị ở cột "`NAMESPACED`" bằng `false`

```bash
[k8s@demo]$ kubectl api-resources -o wide --namespaced=false
---
NAME                    SHORTNAMES   APIVERSION                         NAMESPACED   KIND                VERBS
namespaces              ns           v1                                 false        Namespace           [create delete get list patch update watch]
nodes                   no           v1                                 false        Node                [create delete deletecollection get list patch update watch]
clusters                             management.cattle.io/v3            false        Cluster             [delete deletecollection get list patch create update watch]
nodes                                metrics.k8s.io/v1beta1             false        NodeMetrics         [get list]
ingressclasses                       networking.k8s.io/v1               false        IngressClass        [create delete deletecollection get list patch update watch]
clusterrolebindings                  rbac.authorization.k8s.io/v1       false        ClusterRoleBinding  [create delete deletecollection get list patch update watch]
clusterroles                         rbac.authorization.k8s.io/v1       false        ClusterRole         [create delete deletecollection get list patch update watch]
storageclasses          sc           storage.k8s.io/v1                  false        StorageClass        [create delete deletecollection get list patch update watch]
<output truncated>
```

>NOTE: Các bạn để ý một số resource có tên viết tắt thì có thể sử dụng tên viết tắt (`SHORTNAMES`) cho ngắn gọn.
>
>Ví dụ thay vì bạn phải gõ "`kubectl get deployments`" thì có thể viết gọn là "`kubectl get dp`"

---
# Khi nào nên sử dụng Namespace

`Namespace` thường được sử dụng trong trường hợp trên một K8S Cluster mà có nhiều team khác nhau cùng sử dụng, hoặc triển khai nhiều Project khác nhau.

Lúc này việc tạo `Namespace` sẽ giúp tách biệt các tài nguyên của các team/project với nhau.

Chúng ta cũng có thể cấu hình giới hạn tài nguyên của các `Namespace` tùy vào nhu cầu của từng team/project khác nhau.

---
# Làm việc với Namespace

## Các Namespace mặc định của K8S

Mặc định, sau khi cài đặt một cụm `Kubernetes` thì `Kubernetes` sẽ tạo sẵn 4 `Namespace`

Namespace|Description
---|---
`default`|Khi thao tác với các tài nguyên ở mức namespace mà không chỉ định cụ thể Namespace nào thì mặc định hiểu là thao tác trên Namespace default này.
`kube-system`|Các thành phần Control Plane của K8S được triển khai ở namespace này. Thường thì chúng ta không triển khai các ứng dụng hay workload gì vào namespace này.
`kube-public`|Các tài nguyên được triển khai ở Namespace kube-public có thể được truy cập công khai trong toàn bộ cụm Kubernetes này.
`kube-node-lease`|Cái này mình ít thấy sử dụng nên các bạn có nếu muốn thì tham khảo thêm trên trang chủ của kubernetes nhé!

---
# Namespace và DNS

Sử dụng Service để expose ứng dụng ra bên ngoài k8s.

Tuy nhiên không phải ứng dụng nào cũng cần expose ra ngoài k8s, ví dụ như DB, hay các message queue sử dụng cho internal.

Lúc này các ứng dụng sẽ chỉ cần expose bằng Service dạng `ClusterIP`.

Mỗi `Service` khi được tạo ra thì hệ thống cũng tạo ra một bản ghi `DNS` để lưu thông tin ánh xạ từ server name về service `ClusterIP`. Bản tin `DNS` này có dạng như sau:

>\[service-name\]\.\[namespace-name\]\.svc\.cluster\.local

Có nghĩa là nếu một `Container` trong k8s kết nối tới `service-name` thì nó `service-name` này sẽ được phân giải theo tên service của cùng `namespace` với `Container` đó.

Trong trường hợp muốn kết nối tới service ở namespace khác thì bạn phải sử dụng tên đầy đủ (`FQDN`) của service đó theo format bên trên.

Ví dụ:

ContainerA ở namespace `db` muốn kết nối tới service postgres ở namespace `db` thì nó chỉ cần kết nối tới địa chỉ là "postgres" vì đây là service trong cùng namespace do đó chỉ cần service name là đủ.

ContainerB ở namespace `dev` muốn kết nối tới service postgres ở namespace `db` thì nó phải thực hiện kết nối tới địa chỉ là "postgres.db.svc.cluster.local".
