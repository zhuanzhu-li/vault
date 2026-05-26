---
source-url: inline (user-provided)
captured-date: 2026-05-25
title: "K8s 网络：从子网到 iptables 及关键配置"
author: User (inline content)
---

K8s 网络：从子网到 iptables 及关键配置

先给结论： K8s 集群的子网（Pod/Service CIDR）必须和节点 iptables 规则打通，由 kube-proxy（默认 iptables 模式）维护 NAT / 负载均衡规则，同时内核要开启 bridge-nf-call、ip_forward，否则 Pod 互通、Service 访问会被 iptables 阻断 。

下面从概念、默认网段、iptables 作用、关键配置、常见问题几方面讲清楚。

一、K8s 里的三类 "子网"

节点子网（Node Network）
宿主机物理网卡所在网段，比如 192.168.1.0/24
用于节点间通信、APIServer 访问

Pod 子网（Pod CIDR）
所有 Pod IP 所在大段，由 kubeadm init --pod-network-cidr 指定
典型：
Flannel： 10.244.0.0/16
Calico： 192.168.0.0/16
每个节点再划一段给本节点 Pod，如 10.244.1.0/24

Service 子网（Service CIDR）
ClusterIP 所在网段，默认 10.96.0.0/12
由 --service-cidr 指定，不能和 Pod / 节点网段冲突

二、iptables 在 K8s 里的核心作用

K8s 默认用 kube-proxy + iptables 实现：
Service → Pod 的负载均衡（DNAT）
ClusterIP 虚拟 IP 劫持
NodePort/LoadBalancer 端口转发
Pod 跨节点 / 跨子网 SNAT

1. 核心链路（iptables 模式）
Pod 访问 Service（ClusterIP:Port）
数据包到节点内核，被 KUBE-SERVICES 链劫持
iptables 随机选一个后端 Pod，做 DNAT（改目标 IP 为 Pod IP）
路由到目标 Pod；返回包再做 SNAT

2. kube-proxy 维护的关键 iptables 链
nat 表（核心）
KUBE-SERVICES：匹配 ClusterIP，跳转到对应 Service 链
KUBE-SVC-<hash>：每个 Service 一条，随机转发到 Endpoint
KUBE-SEP-<hash>：每个 Pod Endpoint 一条，做 DNAT

filter 表
KUBE-FORWARD：允许 Pod 间、Pod-节点转发

三、必须开启的内核参数（否则 iptables 不生效）

所有节点都要配置（/etc/sysctl.d/k8s.conf）：

# 让网桥流量过 iptables（CNI 网桥/overlay 必需）
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1

# 开启 IP 转发（Pod 跨节点、Service 访问必需）
net.ipv4.ip_forward = 1

生效：sysctl --system

四、节点防火墙（iptables）放行规则示例

如果节点有自定义 iptables/ufw/firewalld，必须放行 K8s 相关端口与网段，否则会和 kube-proxy 规则冲突。

1. 必放端口（所有节点）

# APIServer（Master）
iptables -A INPUT -p tcp --dport 6443 -j ACCEPT

# kubelet
iptables -A INPUT -p tcp --dport 10250 -j ACCEPT

# NodePort 范围
iptables -A INPUT -p tcp --dport 30000:32767 -j ACCEPT

# Flannel VXLAN（overlay）
iptables -A INPUT -p udp --dport 8472 -j ACCEPT

# Calico BGP（可选）
iptables -A INPUT -p tcp --dport 179 -j ACCEPT

2. 放行 Pod/Service 网段转发

# 允许 Pod 子网（示例 Flannel）
iptables -A FORWARD -s 10.244.0.0/16 -j ACCEPT
iptables -A FORWARD -d 10.244.0.0/16 -j ACCEPT

# 允许 Service 子网
iptables -A FORWARD -s 10.96.0.0/12 -j ACCEPT
iptables -A FORWARD -d 10.96.0.0/12 -j ACCEPT

# 关键：让 kube-proxy 的 KUBE-FORWARD 链优先
iptables -A FORWARD -j KUBE-FORWARD

五、ip-masq-agent：Pod 访问外网的 SNAT

默认 Pod 访问外网会被 MASQUERADE，但集群内网（Pod/Service/节点网段）不伪装。
默认不伪装的网段：10.0.0.0/8、172.16.0.0/12、192.168.0.0/16
自定义：用 ip-masq-agent 配置，避免内网被误 SNAT

六、常见问题与排查

1. Pod 之间 ping 不通
检查内核参数：bridge-nf-call-iptables、ip_forward 是否为 1
检查 iptables FORWARD 链是否放行 Pod CIDR
检查 CNI 插件（Flannel/Calico）是否正常运行

2. 能 ping 通 Pod，但访问 Service（ClusterIP）不通
检查 kube-proxy 是否正常：kubectl get ds -n kube-system kube-proxy
查看 nat 表规则：iptables -t nat -L KUBE-SERVICES -n
确认 Service 有 Endpoint：kubectl get ep <service-name>

3. 节点重启后网络异常
自定义 iptables 规则覆盖了 kube-proxy 规则
解决方案：清空自定义规则，只保留 KUBE- 链，或在 FORWARD 末尾追加 KUBE-FORWARD

七、kube-proxy 模式对比（iptables vs IPVS）

iptables（默认）
优点：兼容性强、无需额外内核模块
缺点：规则线性匹配，大规模集群（>1000 Service）性能下降

IPVS（推荐生产）
优点：哈希表 O(1) 查找、支持更多负载均衡算法（rr/lc/wrr）
缺点：需加载 ip_vs 内核模块

总结
K8s 子网分三类：节点、Pod、Service，网段不能冲突。
iptables 是 K8s 网络核心，由 kube-proxy 维护 NAT / 负载均衡规则。
必须开启 bridge-nf-call、ip_forward，否则 iptables 规则不生效。
节点防火墙要放行 K8s 端口与网段，避免和 kube-proxy 冲突。
大规模集群建议切换到 IPVS 模式提升性能。
