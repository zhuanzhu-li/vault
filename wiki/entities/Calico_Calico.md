---
title: Calico
type: entity
tags: [cni, kubernetes, networking, bgp, network-policy]
aliases:
  - Calico
  - Project Calico
  - Tigera Calico
sources:
  - "[2026-05-25-k8s-networking-subnet-to-iptables](raw/clippings/2026-05-25-k8s-networking-subnet-to-iptables.md)"
---

# Calico

Calico 是 Kubernetes 生态中广泛使用的 CNI 网络插件，支持 BGP 路由模式和丰富网络策略。与 [Flannel](Flannel_Flannel.md) 的 overlay 方式不同，Calico 更适合对网络性能有更高要求的场景。

## 核心特性

- **默认 Pod CIDR**：`192.168.0.0/16`
- **路由模式**：BGP（Border Gateway Protocol），直接在三层路由 Pod 流量，无需 overlay 封装
- **网络策略**：原生支持 Kubernetes NetworkPolicy，实现细粒度安全隔离
- **性能优势**：无 VXLAN 封装开销，网络延迟更低
- **BGP 端口**：TCP 179

## 相关

- [Flannel](Flannel_Flannel.md)
- [Kubernetes 网络子网](Kubernetes%20网络子网_Kubernetes网络子网.md)
- [内核网络参数](内核网络参数_内核网络参数.md)
