---
title: 安装容器运行时 - Containerd
linkTitle: 安装容器运行时 - Containerd
weight: 3
slug: install-cri-containerd
---

## 方法一：从二进制安装包安装
### 安装 runc 和 cni 插件
从 Github 下载 Release 安装包
- [runc](https://github.com/opencontainers/runc/releases)
- [cni plugins](https://github.com/containernetworking/plugins/releases)
- [containerd](https://github.com/containerd/containerd/releases)

### 安装 runc
{{<tabpane>}}
{{<tab header="Linux/amd64" lang="shell">}}
sudo install runc.amd64 /usr/local/sbin/runc
{{</tab>}}
{{</tabpane>}}

### 安装 cni 插件
{{<tabpane>}}
{{<tab header="Linux/amd64" lang="shell">}}
$ mkdir -p /opt/cni/bin
$ tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.1.1.tgz
./
./macvlan
./static
./vlan
./portmap
./host-local
./vrf
./bridge
./tuning
./firewall
./host-device
./sbr
./loopback
./dhcp
./ptp
./ipvlan
./bandwidth
{{</tab>}}
{{</tabpane>}}

### 安装 Containerd
[官方文档](https://github.com/containerd/containerd/blob/main/docs/getting-started.md)
{{<tabpane>}}
{{<tab header="Linux/amd64" lang="shell">}}
$ tar Cxzvf /usr/local containerd-1.6.2-linux-amd64.tar.gz
bin/
bin/containerd-shim-runc-v2
bin/containerd-shim
bin/ctr
bin/containerd-shim-runc-v1
bin/containerd
bin/containerd-stress
{{</tab>}}
{{</tabpane>}}

创建 `config.toml` 配置文件
{{<tabpane>}}
{{<tab header="config.toml" lang="shell">}}
containerd config default > /etc/containerd/config.toml
{{</tab>}}
{{</tabpane>}}

配置 `systemd` cgroup 驱动 
结合 `runc` 使用 `systemd` cgroup 驱动，在 `/etc/containerd/config.toml` 中设置:
{{<tabpane>}}
{{<tab header="Linux/amd64" lang="shell">}}
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
{{</tab>}}
{{</tabpane>}}
 
配置 Systemd
 
下载 [https://raw.githubusercontent.com/containerd/containerd/main/containerd.service](https://raw.githubusercontent.com/containerd/containerd/main/containerd.service) 到 `/usr/local/lib/systemd/system/containerd.service`
{{<tabpane>}}
{{<tab header="Linux/amd64" lang="shell">}}
systemctl daemon-reload
systemctl enable --now containerd
{{</tab>}}
{{</tabpane>}}
 
## 方法二：从 APT 源安装
{{<tabpane>}}
{{<tab header="Debian" lang="shell">}}
# 安装依赖
apt-get update
apt-get install ca-certificates curl gnupg

# 信任 Docker 的 GPG 公钥并添加仓库：
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null

# 安装
apt-get update
apt-get install containerd.io
{{</tab>}}
{{</tabpane>}}