![[ArgoCD-logo.png]]

# Giới thiệu

Đây là một công cụ hỗ trợ công việc tự động cập nhật lại application của chúng ta khi ta thêm hoặc thay đổi config nào đó, nhưng thay vì cập nhật lại toàn bộ như trên thì nó chỉ cập nhật lại những thành phần nào mà có thay đổi config, và tạo thêm thành phần mới nếu ta có thêm file config cho thành phần mới.

![[ArgoCD Flow.png]]

---
# Cài đặt Argocd

## Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Install ArgoCD CLI

```bash
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

![[INFO]]
Đọc thêm: https://argo-cd.readthedocs.io/en/stable/cli_installation/

## Truy cập ArgoCD

Để truy cập ArgoCD thì có thể sử dụng NodePort Service, Ingress.

Ở đây dùng Port Forwarding.

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Mở trình duyệt bất kỳ (Chrome, Edge, ...) và truy cập vào địa chỉ `http://localhost:8080`. 

Vì chạy `localhost` nên khi báo `unsafe` --> Chọn proceed to localhost (unsafe)
![[argocd-install-unsafe.png]]

Tới đây thì sẽ thấy UI như sau
![[ArgoCD-ui.png]]

Với username sẽ là `admin`, và password lấy bằng cách

```bash
$ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Sau khi login sau bạn sẽ thấy được giao diện.
![[ArgoCD-access-ui.png]]

---
# Local User and Accounts

## Tạo user

Trong ArgoCD để tạo tài khoản cho user mới khá đơn giản, ta chỉ cần thao tác với ConfigMap tên là `argocd-cm` ở namespace mà ta cài ArgoCD

Ví dụ cài ở namespace tên là `argocd` thì cấu hình mặc định của `argocd-cm` như sau:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
data:
  url: https://argocd.example.com
  accounts.<username>: <capabilities>

```

>[!INFO]
>Trong đó:
>`<username> `: là tên user cần tạo mới
>`<capabilities>` : Sẽ có hai giá trị là `apiKey` hoặc `login`
>- `apiKey`: cho phép user có thể tạo ra token để truy cập API
>- `login`: cho phép user có thể login vào ArgoCD

>
>Ví dụ: 
>accounts.jason: login

Tạo mật khẩu cho user: jason


## Update lại password

Sử dụng câu lệnh

```bash
argocd login <ARGOCD_SERVER>
```

Với `ARGOCD_SERVER` là `IP:8080`. 

Sau khi login sau thì chạy câu lệnh

```bash
argocd account update-password
```

## Quản lý User

Argo CD CLI cung cấp bộ lệnh để đặt mật khẩu người dùng và tạo tokens.

Danh sách người dùng đầy đủ

```bash
argocd account list
```

Thông tin chi tiết cụ thể về người dùng

```bash
argocd account get --account <username>
```

Đặt mật khẩu người dùng

```bash
argocd account update-password --account <name> --current-password <current-user-password> --new-password <new-user-password>
```

> [!INFO] Nếu bạn đang quản lý người dùng với tư cách là người dùng quản trị, thì `<current-user-password>` phải là mật khẩu quản trị hiện tại.

Tạo auth token

```bash
argocd account generate-token --account <username>
```

> [!INFO] Nếu flag `--account` bị bỏ qua thì ArgoCD tạo token cho người dùng hiện tại.

## Failed logins rate limiting

ArgoCD từ chối các nỗ lực đăng nhập sau khi quá nhiều thất bại để ngăn chặn việc ép buộc mật khẩu.

Các biến môi trường sau đây có sẵn để kiểm soát cài đặt điều tiết:

Variable|Detail|Default
---|---|---
`ARGOCD_SESSION_FAILURE_MAX_FAIL_COUNT`|Số lần đăng nhập thất bại tối đa trước khi ArgoCD bắt đầu từ chối các lần đăng nhập|5
`ARGOCD_SESSION_FAILURE_WINDOW_SECONDS`|Số giây cho cửa sổ thất bại. Nếu thiết lập là `0`, cửa sổ lỗi sẽ bị vô hiệu hóa và các lần đăng nhập sẽ bị từ chối sau 10 lần đăng nhập liên tiếp, bất kể chúng đã xảy ra như thế nào.|300 (5 minutes)
`ARGOCD_SESSION_MAX_CACHE_SIZE`|Số lượng mục nhập tối đa được phép trong bộ nhớ cache.|1000
`ARGOCD_MAX_CONCURRENT_LOGIN_REQUESTS_COUNT`|Giới hạn số lượng yêu cầu đăng nhập đồng thời tối đa. Nếu thiết lập là `0` thì giới hạn sẽ bị vô hiệu hóa.|50

## Phân quyền (RBAC)

### Basic built-in roles

RBAC cơ bản gồm 2 quyền:

Parameter|Mean
---|---
`role:readonly`|là quyền truy cập chỉ đọc vào tất cả các tài nguyên
`role:admin`|là quyền truy cập không hạn chế vào tất cả các tài nguyên

Để phân quyền cho user thì ta sẽ thao tác với ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  # policy.default: role:admin
  policy.csv: |

```

Ta sẽ định nghĩa quyền ở trong trường `policy.csv` với định dạng.

Parameter|Mean
---|---
`p, <role/user/group\>, <resource>, <action>, <object>`|Tất cả các tài nguyên _ngoại trừ_ các quyền dành riêng cho ứng dụng
`p, <role/user/group>, <resource>, <action>, <appproject>/<object>`|Các ứng dụng, nhật ký và điều hành (thuộc về một AppProject)

Trong đó

Parameter|Mean
---|---
`<role/user/group>`|là tên của role hoặc user hoặc group.
`<resource>`|có các giá trị `clusters`, `projects`, `applications`, `repositories`, `certificates`, `accounts`, `gpgkeys`, `logs`, `exec`.
`<action>`|có các giá trị `get`, `create`, `update`, `delete`, `sync`, `override`, `action/<group/kind/action-name>`

Ví dụ muốn user là `jason` có quyền restart được Deployment thì sẽ định dạng

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  policy.csv: |
    p, jason, applications, action/apps/Deployment/restart, default/*, allow

```

Các roles và groups bổ sung có thể được định cấu hình trong `argocd-rbac-cm` ConfigMap. 

Ví dụ dưới đây định cấu hình một role tùy chỉnh, được đặt tên là `org-admin`. 

Roles được gán cho bất kỳ người dùng nào thuộc `your-github-org:your-team` group. 

Tất cả những người dùng khác nhận được chính sách mặc định là `role:readonly`, không thể sửa đổi cài đặt ArgoCD.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  policy.csv: |
    p, role:org-admin, applications, *, */*, allow
    p, role:org-admin, clusters, get, *, allow
    p, role:org-admin, repositories, get, *, allow
    p, role:org-admin, repositories, create, *, allow
    p, role:org-admin, repositories, update, *, allow
    p, role:org-admin, repositories, delete, *, allow
    p, role:org-admin, logs, get, *, allow
    p, role:org-admin, exec, create, */*, allow
    g, your-github-org:your-team, role:org-admin
```

Ví dụ này xác định _role_ là `staging-db-admins` với _chín quyền_ cho phép vai trò đó thực hiện các _hành động_ (`create`/`delete`/`get`/`override`/`sync`/`update` applications, `get` logs, `create` exec and `get` appprojects) đối với  `*` (all) đối tượng trong `staging-db-admins` Argo CD AppProject.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  policy.csv: |
    p, role:staging-db-admins, applications, create, staging-db-admins/*, allow
    p, role:staging-db-admins, applications, delete, staging-db-admins/*, allow
    p, role:staging-db-admins, applications, update, staging-db-admins/*, allow
    p, role:staging-db-admins, applications, get, staging-db-admins/*, allow
    p, role:staging-db-admins, applications, override, staging-db-admins/*, allow
    p, role:staging-db-admins, applications, sync, staging-db-admins/*, allow
    p, role:staging-db-admins, logs, get, staging-db-admins/*, allow
    p, role:staging-db-admins, exec, create, staging-db-admins/*, allow
    p, role:staging-db-admins, projects, get, staging-db-admins, allow
    g, db-admins, role:staging-db-admins
```

### Xác thực chính sách

Xác thực chính sách được lưu trữ trong tệp `.csv`

```bash
argocd admin settings rbac validate --policy-file somepolicy.csv
```

Xác thực chính sách được lưu trữ trong ConfigMap

```bash
argocd admin settings rbac validate --policy-file argocd-rbac-cm.yaml
```

Xác thực một chính sách được lưu trữ trong K8s, được ArgoCD sử dụng trong namespace `argocd` , đảm bảo rằng bối cảnh hiện tại là `~/.kube/config` trỏ đến cụm ArgoCD và cung cấp namespace thích hợp

```bash
argocd admin settings rbac validate --namespace argocd
```

### Kiểm tra chính sách

Để kiểm tra xem role hoặc đối tượng (nhóm hoặc người dùng cục bộ) có đủ quyền để thực hiện các hành động nhất định trên một số tài nguyên nhất định hay không

```bash
argocd admin settings rbac can SOMEROLE ACTION RESOURCE SUBRESOURCE [flags]
```

Đưa ra ví dụ từ ConfigMap ở trên, xác định role `role:org-admin`, và được lưu trữ trên local system dưới dạng `argocd-rbac-cm-yaml`, kiểm tra xem vai trò đó có thể làm điều gì đó như sau hay không

```bash
argocd admin settings rbac can role:org-admin get applications --policy-file argocd-rbac-cm.yaml
argocd admin settings rbac can role:org-admin get clusters --policy-file argocd-rbac-cm.yaml
argocd admin settings rbac can role:org-admin create clusters 'somecluster' --policy-file argocd-rbac-cm.yaml
argocd admin settings rbac can role:org-admin create applications 'someproj/someapp' --policy-file argocd-rbac-cm.yaml
```

Tương tự

```bash
argocd admin settings rbac can role:staging-db-admins get applications --policy-file policy.csv
argocd admin settings rbac can role:staging-db-admins get applications 'staging-db-admins/*' --policy-file policy.csv
argocd admin settings rbac can role:staging-db-admins get applications --policy-file policy.csv --default-role role:readonly
argocd admin settings rbac can db-admins get applications 'staging-db-admins/*' --policy-file policy.csv
```

---
# Kết nối Private Repositories

Để kết nối với `Private Repositories`

Chọn icon `Settings` --> `Repositories`.
![[ArgoCD-Repo-conn.png]]

Có 3 loại kết nối

Type|Mean
---|---
`CONNECT REPO USING SSH`|Sử dụng SSH để kết nối Repository
`CONNECT REPO USING HTTPS`|Sử dụng HTTPS để kết nối Repository
`CONNECT REPO USING GITHUB APP`|Sử dụng GITHUB APP để kết nối Repository

![[ArgoCD-repo-conn-2.png]]

Ví dụ chọn `CONNECT REPO USING HTTPS` --> Điền thông tin vào --> `CONNECT`
![[ArgoCD-repo-conn-3.png]]

---
# Tạo APP

Để kết nối `ArgoCD` tới `kubernetes cluster` và `git repo`, ta cần tạo một APP trên ArgoCD. 

Nhấn vào nút `+ NEW APP` ở trên UI.
![[ArgoCD-create-app-1.png]]

form GENARAL

Parameter|Value|Notes
---|---|---
Application Name|nodejs-microservice|Tên APP
Project|default|Chọn Project
SYNC POLICY|Automatic|Manual (khi push code lên Git, phải vào bấm bằng tay để APP cập nhật lại theo template config mới) hoặc Automatic (tự động)

![[ArgoCD-create-app-2.png]]

form SOURCE

Parameter|Value|Note
---|---|---
Repository URL|https://github.com/hoalongnatsu/microservices.git|Đường dẫn tới Git repo mà bạn chứa template config của ứng dụng
Revision|HEAD|Chọn Branch của Git
Path|k8s|Tên folder chứa file kubernetes config của APP, nếu nằm ở root thì điền vào đường dẫn là /

![[ArgoCD-create-app-3.png]]

form DESTINATION

Parameter|Value|Note
---|---|---
Cluster URL|https://kubernetes.default.svc|Đường dẫn tới K8S Cluster
Namespace|default|Chọn Namespace để triển khai

![[ArgoCD-create-app-4.png]]

Sau khi điền sau hết --> Chọn `Create`, lúc này `ArgoCD` sẽ tạo một APP và tiến hành deploy APP lên trên K8S Cluster.
![[ArgoCD-create-app-5.png]]

---
# Xóa APP

Để xóa APP trên `ArgoCD` rất đơn giản

Ở giao diện quản lý APP --> chọn `icon X` hoặc chọn APP cần xóa --> `Delete`
![[ArgoCD-Del.png]]