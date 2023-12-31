---
title: 常用下载镜像
description: 
weight: 100
---

|组件|地址|
|---|---|
|Kubernetes|https://mirrors.tuna.tsinghua.edu.cn/kubernetes/|
|Kubernetes|https://mirrors.ustc.edu.cn/kubernetes/|
|Kubernetes二进制|https://www.downloadkubernetes.com/|

## kubernetes APT 源配置
{{<tabpane>}}
{{<tab header="清华 tuna" lang="shell">}}
apt-get update && \
    apt-get install -y apt-transport-https ca-certificates curl && \
    curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://mirrors.tuna.tsinghua.edu.cn/kubernetes/apt kubernetes-xenial main" | tee /etc/apt/sources.list.d/kubernetes.list && \
    apt-get update
{{</tab>}}
{{<tab header="中科大 USTC" lang="shell">}}
apt-get update && \
    apt-get install -y apt-transport-https ca-certificates curl && \
    curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://mirrors.ustc.edu.cn/kubernetes/apt kubernetes-xenial main" | tee /etc/apt/sources.list.d/kubernetes.list && \
    apt-get update
{{</tab>}}
{{</tabpane>}}

## 常用脚本
{{<tabpane>}}
{{<tab header="kubernetes 卸载" lang="shell">}}
kubeadm reset
sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni kube*   
sudo apt-get autoremove  
sudo rm -rf ~/.kube
{{</tab>}}
{{</tabpane>}}
{{<tabpane>}}
{{<tab header="kubernetes 安装" lang="shell">}}
apt-mark unhold kubeadm kubelet kubectl && \
apt-get update && apt-get install -y kubeadm=1.27.6-00 kubelet=1.27.6-00 kubectl=1.27.6-00 && \
apt-mark hold kubelet kubectl
{{</tab>}}
{{</tabpane>}}
