---
title: 高可用集群
linkTitle: 高可用集群
weight: 5
slug: init-ha-cluster-with-kubeadm
resources:
- src: haproxy.png
  params:
    byline: ""
---

# kube-apiserver 创建负载均衡器

针对非云环境，使用软件负载平衡选项，这里以 haproxy 为例子

## 在负载平衡机器节点安装 haproxy
{{<tabpane>}}
{{<tab header="Debian 11 / haproxy 2.4 (LTS)" lang="shell">}}
# 配置安装源
curl https://haproxy.debian.net/bernat.debian.org.gpg \
      | gpg --dearmor > /usr/share/keyrings/haproxy.debian.net.gpg
echo deb "[signed-by=/usr/share/keyrings/haproxy.debian.net.gpg]" \
      http://haproxy.debian.net bullseye-backports-2.4 main \
      > /etc/apt/sources.list.d/haproxy.list

# 安装
apt-get update
apt-get install haproxy=2.4.\*
{{</tab>}}
{{</tabpane>}}

## 创建 haproxy 配置文件
{{<tabpane>}}
{{<tab header="/etc/haproxy/haproxy.cfg" lang="shell">}}
# Global settings
#---------------------------------------------------------------------
global
    log /dev/log local0
    log /dev/log local1 notice
    daemon

#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 1
    timeout http-request    10s
    timeout queue           20s
    timeout connect         5s
    timeout client          20s
    timeout server          20s
    timeout http-keep-alive 10s
    timeout check           10s

#---------------------------------------------------------------------
# apiserver frontend which proxys to the control plane nodes
#---------------------------------------------------------------------
frontend apiserver
    bind *:8443
    mode tcp
    option tcplog
    default_backend apiserverbackend

#---------------------------------------------------------------------
# round robin balancing for apiserver
#---------------------------------------------------------------------
backend apiserverbackend
    option httpchk GET /healthz
    http-check expect status 200
    mode tcp
    option ssl-hello-chk
    balance     roundrobin
        server k8s-api1 10.206.0.13:6443 check
        server k8s-api2 10.206.0.14:6443 check
        server k8s-api3 10.206.0.15:6443 check
{{</tab>}}
{{</tabpane>}}

* 参考 [【软件负载平衡选项指南】](https://git.k8s.io/kubeadm/docs/ha-considerations.md#options-for-software-load-balancing)

重启 haproxy
```
systemctl restart haproxy
```
运行结果如下
 
{{% imgproc haproxy Fit "600x400" %}}{{% /imgproc %}}

