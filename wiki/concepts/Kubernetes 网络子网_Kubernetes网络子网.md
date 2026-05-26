---
title: Kubernetes 网络子网
type: concept
tags: [kubernetes, networking, cidr, pod, service]
aliases:
  - Kubernetes 网络子网
  - K8s 子网
  - Pod CIDR
  - Service CIDR
sources:
  - "[2026-05-25-k8s-networking-subnet-to-iptables](raw/clippings/2026-05-25-k8s-networking-subnet-to-iptables.md)"
---

# Kubernetes 网络子网

Kubernetes 集群中存在三类互不重叠的逻辑子网：节点子网、Pod 子网和 Service 子网。三者必须通过 [kube-proxy iptables 模式](kube-proxy%20iptables%20模式_kube-proxy%20iptables模式.md) 或 [kube-proxy IPVS 模式](kube-proxy%20IPVS%20模式_kube-proxy%20IPVS模式.md) 的 NAT/转发规则打通。

## 子网类型

### 节点子网（Node Network）

宿主机物理网卡所在网段，例如 `192.168.1.0/24`。用于节点间通信和 APIServer 访问。

### Pod 子网（Pod CIDR）

所有 Pod IP 所属的大段，由 `kubeadm init --pod-network-cidr` 指定。CNI 插件会在每个节点上进一步划分子段分配给本节点 Pod：

- **[Flannel](Flannel_Flannel.md)**：默认 `10.244.0.0/16`，每节点 `/24`
- **[Calico](Calico_Calico.md)**：默认 `192.168.0.0/16`

### Service 子网（Service CIDR）

ClusterIP 所属网段，默认 `10.96.0.0/12`。由 `--service-cidr` 指定，**不能与 Pod 和节点网段冲突**。

## 约束

三类子网的网段必须互不重叠，否则 kube-proxy 的 DNAT/SNAT 规则会产生路由冲突。

## 相关

- [kube-proxy iptables 模式](kube-proxy%20iptables%20模式_kube-proxy%20iptables模式.md)
- [kube-proxy IPVS 模式](kube-proxy%20IPVS%20模式_kube-proxy%20IPVS模式.md)
- [内核网络参数](内核网络参数_内核网络参数.md)
- [Flannel](Flannel_Flannel.md)
- [Calico](Calico_Calico.md)
