---
title: Kubeadm 引导集群
linkTitle: Kubeadm 引导集群
weight: 4
slug: init-cluster-with-kubeadm
---
## 初始化控制面节点
{{<card code=true lang="shell">}}
kubeadm init
{{</card>}}
## 安装网络插件
网络插件列表
1. [Weave](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/)
 
{{<tabpane>}}
{{<tab header="可选" disabled=true />}}
{{<tab header="Weave" lang="shell">}}
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
{{</tab>}}
{{</tabpane>}}