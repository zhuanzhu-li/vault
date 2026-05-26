---
title: kube-proxy IPVS 模式
type: concept
tags: [kubernetes, networking, ipvs, kube-proxy, load-balancing]
aliases:
  - kube-proxy IPVS 模式
  - K8s IPVS 模式
  - IPVS
sources:
  - "[2026-05-25-k8s-networking-subnet-to-iptables](raw/clippings/2026-05-25-k8s-networking-subnet-to-iptables.md)"
---

# kube-proxy IPVS 模式

Kubernetes 网络代理的替代模式，使用 Linux IPVS（IP Virtual Server）代替 iptables 实现 Service 负载均衡。适用于大规模生产集群。

## 工作原理

IPVS 在内核中使用哈希表（O(1) 查找）而非 iptables 的线性规则匹配，显著提升 Service 数量大时的性能。

## 对比 iptables 模式

| 维度 | iptables | IPVS |
|------|----------|------|
| 查找复杂度 | O(n) 线性匹配 | O(1) 哈希查找 |
| 负载均衡算法 | 仅随机 | rr、lc、wrr 等多种算法 |
| 内核模块 | 无需额外加载 | 需加载 `ip_vs` 模块 |
| 适用规模 | 中小规模（<1000 Service） | 大规模（>1000 Service） |
| 兼容性 | 极高 | 需内核支持 |

## 相关

- [kube-proxy iptables 模式](kube-proxy%20iptables%20模式_kube-proxy%20iptables模式.md)
- [Kubernetes 网络子网](Kubernetes%20网络子网_Kubernetes网络子网.md)
- [内核网络参数](内核网络参数_内核网络参数.md)
