# 1 - Cài đặt Container Runtime và Cấu hình Cgroup driver

>[!INFO] Cài đặt trên tất cả các Node (Master + Worker)

Sử dụng `ContainerD` làm `Container Runtime`

Tạo script để cài đặt `ContainerD` và Cấu hình `Cgroup driver`

```bash
vi install-containerd-cgroup.sh
```

```bash
#!/bin/bash
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

sudo sysctl --system
sudo apt-get install containerd -y
mkdir /etc/containerd
containerd config default > /etc/containerd/config.toml
sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
systemctl restart containerd
```

```bash
bash install-containerd-cgroup.sh
```

>Đọc thêm:
>- https://kubernetes.io/docs/setup/production-environment/container-runtimes/#forwarding-ipv4-and-letting-iptables-see-bridged-traffic
>- https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd-systemd

---
#  2 - Cài đặt kubeadm, kubelet, kubectl

>[!INFO] Cài đặt trên tất cả các Node (Master + Worker)

Tạo scripts để cài đặt
- kubeadm
- kubelet
- kubectl

```bash
vi install-kubeadm-kubelet-kubectl.sh
```

```bash
#!/bin/bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

```bash
bash install-kubeadm-kubelet-kubectl.sh
```

>Đọc thêm: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl

---
# 3 - Cài đặt LoadBalancer

>[!INFO] Cài đặt trên Bastion

Sử dụng `NGINX` làm `LoadBalancer`

Tạo script cài đặt `NGINX` và Cấu hình `LoadBalancer`

```bash
vi install-nginx-lb.sh
```

```bash
echo '192.168.1.21 master1' >> /etc/hosts
echo '192.168.1.22 master2' >> /etc/hosts
echo '192.168.1.23 master3' >> /etc/hosts
sudo apt-get update
sudo apt-get install nginx -y
mkdir -p /etc/nginx/k8s-lb.d/
cat <<EOF | sudo tee /etc/nginx/k8s-lb.d/apiserver.conf
stream {
    upstream kubernetes {
        server master1:6443 max_fails=3 fail_timeout=30s;
        server master2:6443 max_fails=3 fail_timeout=30s;
        server master3:6443 max_fails=3 fail_timeout=30s;
    }
server {
        listen 6443;
        proxy_pass kubernetes;
    }
}
EOF
echo 'include /etc/nginx/k8s-lb.d/*;' >> /etc/nginx/nginx.conf
nginx -s reload
```

```bash
bash install-nginx-lb.sh
```

>Chú ý:
>master1: là IP của Master Node 1
>master2: là IP của Master Node 2
>master3: là IP của Master Node 3

---
# 4 - Init Cluster

>[!INFO] Cài đặt trên Master Node 1

```bash
echo '192.168.1.10 apiserver.lb' >> /etc/hosts
kubeadm init --control-plane-endpoint=apiserver.lb:6443 \
--upload-certs \
--pod-network-cidr=10.0.0.0/8
```

>Chú ý:
>`apiserver.lb `là `IP` của `LoadBalancer`

---
# 5 - Cài đặt Container Network Interface (CNI)

>[!INFO] Cài đặt trên Master Node 1

Sử dụng `Cilium` làm `CNI`

Sử dụng `Helm` để cài đặt `Cilium`

Tạo scripts cài đặt `Helm` và `Cilium`

```bash
vi install-helm-cilium.sh
```

```bash
#!/bin/bash

# Install Helm
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm -y

# Instal Cilium
helm repo add cilium https://helm.cilium.io/
helm install cilium cilium/cilium --version 1.11.6 --namespace kube-system
```

```bash
bash install-helm-cilium.sh
```

---
# 6 - Join Node

## Add Master Node

>[!INFO] Áp dụng trên tất cả Master Node

```bash
echo '192.168.1.10 apiserver.lb' >> /etc/hosts
kubeadm join apiserver.lb:6443 --token oylvmu.12pwimke0blaaeji \
--discovery-token-ca-cert-hash sha256:303a791ef0bdaeb3a3b54ca80f8f4831dff6d0bb1c43c664d9102c9ec569ef61 \
--control-plane --certificate-key 3b4da12cd25d1c1e7a47abcb908c73405c4abd5e542f99692d8f1b9d368d307a
```

>Chú ý:
>apiserver.lb: là IP của LoadBalancer

## Add Worker Node

>[!INFO] Áp dụng trên tất cả Worker Node

```bash
echo '192.168.1.10 apiserver.lb' >> /etc/hosts
kubeadm join apiserver.lb:6443 --token 9vr73a.a8uxyaju799qwdjv \
--discovery-token-ca-cert-hash sha256:7c2e69131a36ae2a042a339b33381c6d0d43887e2de83720eff5359e26aec866
```

>Chú ý:
>apiserver.lb: là IP của LoadBalancer
