---
layout: post
read_time: true
show_date: true
title: "Debug kubernetes 组件"
date: 2022-10-27
img: posts/20221027/kubernetes-debug.png
tags: [kubernetes, debug]
author: Mengjiao Liu
description: "在 Goland 上打断点调试 kubernetes 组件"
---
本文涉及：

- goland
- dlv
- kind
- kubernetes

# 背景
因为经常需要修 k8s bug，复现相关 issue，调试 kubernetes 组件就很有必要。

# 环境准备

本地：准备 golang 环境、git 环境、goland IDE、dlvs(go-delve)远端：kubernetes集群、安装dlv(go-delve)

## 使用 kind启动一个 k8s 集群

如果已经有部署好的集群，忽略这一步

我平常测试一般使用 kind 启用一个 k8s 集群，比较快速，因为 kind 启用的集群默认不会暴露除了 kube-apiserver 以外的端口，所以需要我们自定义dlv需要暴露的端口，默认 2345

暴露 2345 端口

```bash
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 2345
    hostPort: 2345
```

```bash
kind create cluster --name=cluster-1-25-3 --image=kindest/node:v1.25.3 --wait=5m   -v=3 --config=/root/kind/dlv.yaml
```

## 将需要 debug 的k8s 组件停止

```bash
# 直接将其从静态 pod 目录移走即可（kubeadm 方式安装的集群）
mv /etc/kubernetes/manifests/<对应组件> /root

# 看下是否还存在对应组件
crictl ps
```

## kind 集群 node 内安装 dlv 和 go 环境

```bash
apt update
apt-get install git -y

apt install golang -y

export GOPROXY="https://goproxy.io"
go install github.com/go-delve/delve/cmd/dlv@latest

# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

export PATH=$PATH:/root/go/bin
```

## 获取kubernetes代码

- 拉取代码

本地拉取：

```
git clone https://github.com/kubernetes/kubernetes.git
```

- checkout到对应的分支

首先查看kubernetes集群版本：

```
Client Version: version.Info{Major:"1", Minor:"24", GitVersion:"v1.24.3", GitCommit:"aef86a93758dc3cb2c658dd9657ab4ad4afc21cb", GitTreeState:"clean", BuildDate:"2022-07-13T14:30:46Z", GoVersion:"go1.18.3", Compiler:"gc", Platform:"linux/amd64"}
Kustomize Version: v4.5.4
Server Version: version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.2", GitCommit:"5835544ca568b757a8ecae5c153f317e5736700e", GitTreeState:"clean", BuildDate:"2022-09-22T05:25:21Z", GoVersion:"go1.19.1", Compiler:"gc", Platform:"linux/amd64"}
```

切换到对应版本：

```
git checkout origin/release-1.25
```

# 编译

```bash
make kube-apiserver DBG=1 KUBE_BUILD_PLATFORMS=linux/amd64
```

等价于 ==

```bash
1. 编译开始前，我们需要在hack/lib/golang.sh 行 843 的 goldflags 变量中去掉 -s -w
否则会报错
 "could not launch process: could not open debug info - debuggee must not be built with 'go run' or -ldflags='-s -w', which strip debug info" 

2. make kube-apiserver GOGCFLAGS="-N -l"  KUBE_BUILD_PLATFORMS=linux/amd64
```

二进制文件

```bash
ls _output/bin/
```

# 拷贝二进制文件到远端

```bash
docker cp kube-apiserver  cluster-1-25-3-control-plane:/root

docker exec -it  cluster-1-25-3-control-plane ls -l /root/kube-apiserver
-rwxr-xr-x 1 root root 146202624 Oct 27 09:46 /root/kube-apiserver

docker exec -it  cluster-1-25-3-control-plane bash
```

获取 `kube-apiserver` 部署参数

我们可以从集群中获取

```yaml
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 172.18.0.3:6443
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - --kube-apiserver
    - --advertise-address=172.18.0.3
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --enable-admission-plugins=NodeRestriction
    - --enable-bootstrap-token-auth=true
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
    - --etcd-servers=https://127.0.0.1:2379
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
    - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
    - --requestheader-allowed-names=front-proxy-client
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --requestheader-extra-headers-prefix=X-Remote-Extra-
    - --requestheader-group-headers=X-Remote-Group
    - --requestheader-username-headers=X-Remote-User
    - --runtime-config=
    - --secure-port=6443
    - --service-account-issuer=https://kubernetes.default.svc.cluster.local
    - --service-account-key-file=/etc/kubernetes/pki/sa.pub
    - --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
    - --service-cluster-ip-range=10.96.0.0/16
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key**
    image: registry.k8s.io/kube-apiserver:v1.25.3
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 172.18.0.3
        path: /livez
        port: 6443
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    name: kube-apiserver
    readinessProbe:
      failureThreshold: 3
      httpGet:
        host: 172.18.0.3
        path: /readyz
        port: 6443
        scheme: HTTPS
      periodSeconds: 1
      timeoutSeconds: 15
    resources:
      requests:
        cpu: 250m
    startupProbe:
      failureThreshold: 24
      httpGet:
        host: 172.18.0.3
        path: /livez
        port: 6443
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    volumeMounts:
    - mountPath: /etc/ssl/certs
      name: ca-certs
      readOnly: true
    - mountPath: /etc/ca-certificates
      name: etc-ca-certificates
      readOnly: true
    - mountPath: /etc/kubernetes/pki
      name: k8s-certs
      readOnly: true
    - mountPath: /usr/local/share/ca-certificates
      name: usr-local-share-ca-certificates
      readOnly: true
    - mountPath: /usr/share/ca-certificates
      name: usr-share-ca-certificates
      readOnly: true
  hostNetwork: true
  priorityClassName: system-node-critical
  securityContext:
    seccompProfile:
      type: RuntimeDefault
  volumes:
  - hostPath:
      path: /etc/ssl/certs
      type: DirectoryOrCreate
    name: ca-certs
  - hostPath:
      path: /etc/ca-certificates
      type: DirectoryOrCreate
    name: etc-ca-certificates
  - hostPath:
      path: /etc/kubernetes/pki
      type: DirectoryOrCreate
    name: k8s-certs
  - hostPath:
      path: /usr/local/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-local-share-ca-certificates
  - hostPath:
      path: /usr/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-share-ca-certificates
status: {}
```

我们其实需要的是 command 下面的这部分

```yaml
--advertise-address=172.18.0.3
--allow-privileged=true
--authorization-mode=Node,RBAC
--client-ca-file=/etc/kubernetes/pki/ca.crt
--enable-admission-plugins=NodeRestriction
--enable-bootstrap-token-auth=true
--etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
--etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
--etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
--etcd-servers=https://127.0.0.1:2379
--kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
--kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
--kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
--proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
--proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
--requestheader-allowed-names=front-proxy-client
--requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
--requestheader-extra-headers-prefix=X-Remote-Extra-
--requestheader-group-headers=X-Remote-Group
--requestheader-username-headers=X-Remote-User
--runtime-config=
--secure-port=6443
--service-account-issuer=https://kubernetes.default.svc.cluster.local
--service-account-key-file=/etc/kubernetes/pki/sa.pub
--service-account-signing-key-file=/etc/kubernetes/pki/sa.key
--service-cluster-ip-range=10.96.0.0/16
--tls-cert-file=/etc/kubernetes/pki/apiserver.crt
--tls-private-key-file=/etc/kubernetes/pki/apiserver.key
```

# dlv 的方式部署

```bash
dlv exec --listen=:2345 --headless=true --api-version=2 /root/kube-apiserver -- \
--advertise-address=172.18.0.3 \
--allow-privileged=true \
--authorization-mode=Node,RBAC \
--client-ca-file=/etc/kubernetes/pki/ca.crt \
--enable-admission-plugins=NodeRestriction \
--enable-bootstrap-token-auth=true \
--etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt \
--etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt \
--etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key \
--etcd-servers=https://127.0.0.1:2379 \
--kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt \
--kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key \
--kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname \
--proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt \
--proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key \
--requestheader-allowed-names=front-proxy-client \
--requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt \
--requestheader-extra-headers-prefix=X-Remote-Extra- \
--requestheader-group-headers=X-Remote-Group \
--requestheader-username-headers=X-Remote-User \
--runtime-config= \
--secure-port=6443 \
--service-account-issuer=https://kubernetes.default.svc.cluster.local \
--service-account-key-file=/etc/kubernetes/pki/sa.pub \
--service-account-signing-key-file=/etc/kubernetes/pki/sa.key \
--service-cluster-ip-range=10.96.0.0/16 \
--tls-cert-file=/etc/kubernetes/pki/apiserver.crt \
--tls-private-key-file=/etc/kubernetes/pki/apiserver.key \
```

# goland 配置 remote host & 打断点

之后在 goland 配置连接到远程主机

在本地代码上打上断点，就可以 debug 了。

# 参考
[https://www.kevinwu0904.top/blogs/k8s-development/](https://www.kevinwu0904.top/blogs/k8s-development/)

[https://github.com/kubernetes/kubernetes/issues/77527](https://github.com/kubernetes/kubernetes/issues/77527)

[https://github.com/go-delve/delve/issues/1698](https://github.com/go-delve/delve/issues/1698)