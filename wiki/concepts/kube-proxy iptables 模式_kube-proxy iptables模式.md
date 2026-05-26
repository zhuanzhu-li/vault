---
title: kube-proxy iptables 模式
type: concept
tags: [kubernetes, networking, iptables, kube-proxy, nat]
aliases:
  - kube-proxy iptables 模式
  - K8s iptables 模式
  - KUBE-SERVICES
sources:
  - "[2026-05-25-k8s-networking-subnet-to-iptables](raw/clippings/2026-05-25-k8s-networking-subnet-to-iptables.md)"
---

# kube-proxy iptables 模式

Kubernetes 默认的网络代理模式，由 kube-proxy 在节点上维护 iptables 规则，实现 Service 的负载均衡、ClusterIP 劫持、NodePort 端口转发和 Pod 跨子网 SNAT。

## 核心链路

Pod 访问 Service（ClusterIP:Port）的完整路径：

1. 数据包到达节点内核，被 `KUBE-SERVICES` 链劫持
2. iptables 根据负载均衡策略随机选择一个后端 Pod，执行 DNAT（将目标 IP 改为 Pod IP）
3. 路由到目标 Pod
4. 返回包执行 SNAT（源地址转换）

## 关键 iptables 链

### nat 表（核心）

| 链 | 作用 |
|----|------|
| `KUBE-SERVICES` | 匹配 ClusterIP，跳转到对应 Service 链 |
| `KUBE-SVC-<hash>` | 每个 Service 一条链，随机转发到 Endpoint |
| `KUBE-SEP-<hash>` | 每个 Pod Endpoint 一条链，执行 DNAT |

### filter 表

| 链 | 作用 |
|----|------|
| `KUBE-FORWARD` | 允许 Pod 间、Pod 到节点的流量转发 |

## 优缺点

- **优点**：兼容性强，无需额外内核模块
- **缺点**：规则线性匹配，大规模集群（>1000 Service）性能下降

## 相关

- [Kubernetes 网络子网](Kubernetes%20网络子网_Kubernetes网络子网.md)
- [kube-proxy IPVS 模式](kube-proxy%20IPVS%20模式_kube-proxy%20IPVS模式.md)
- [内核网络参数](内核网络参数_内核网络参数.md)
- [Flannel](Flannel_Flannel.md)
- [Calico](Calico_Calico.md)
