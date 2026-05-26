---
title: Flannel
type: entity
tags: [cni, kubernetes, networking, overlay]
aliases:
  - Flannel
  - flannel
  - CoreOS Flannel
sources:
  - "[2026-05-25-k8s-networking-subnet-to-iptables](raw/clippings/2026-05-25-k8s-networking-subnet-to-iptables.md)"
---

# Flannel

Flannel 是由 CoreOS 开发的轻量级 CNI 网络插件，为 Kubernetes 集群提供简单的 overlay 网络，默认使用 VXLAN 封装实现 Pod 跨节点通信。

## 核心特性

- **默认 Pod CIDR**：`10.244.0.0/16`
- **每节点子网**：`/24`（即 `10.244.1.0/24`、`10.244.2.0/24` 等）
- **封装协议**：VXLAN（UDP 8472），也支持 host-gw、wireguard 等后端
- **简单可靠**：专注网络连通性，不涉及网络策略

## 相关

- [Calico](Calico_Calico.md)
- [Kubernetes 网络子网](Kubernetes%20网络子网_Kubernetes网络子网.md)
- [内核网络参数](内核网络参数_内核网络参数.md)
