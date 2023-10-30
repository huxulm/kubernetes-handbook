---
title: 节点环境准备
linkTitle: 节点环境准备
weight: 1
slug: node-prepare
---

# 防火墙规则
Master节点
{{% pageinfo %}}
2379,6443,10250,10251,10252 
{{% /pageinfo %}}

Worker节点
{{% pageinfo %}}
10250,30000-32767
{{% /pageinfo %}}

# 禁用交换分区 | 桥接流量
{{< tabpane >}}
{{<tab header="Master & Worker" disabled=true />}}
{{<tab header="禁用交换分区" lang="shell">}}
swapoff -a
sed -i.bak -r 's/(.+ swap .+)/#\1/' /etc/fstab
{{< /tab >}}
{{< /tabpane >}}

{{<tabpane>}}
{{<tab header="Master & Worker" disabled=true />}}
{{<tab header="桥接" lang="shell">}}
lsmod | grep br_netfilter 
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system
{{</tab>}}
{{</tabpane>}}