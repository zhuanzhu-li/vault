---
title: ip-masq-agent
type: concept
tags: [kubernetes, networking, snat, ip-masq-agent]
aliases:
  - ip-masq-agent
  - IP MASQ Agent
  - Pod SNAT
sources:
  - "[2026-05-25-k8s-networking-subnet-to-iptables](raw/clippings/2026-05-25-k8s-networking-subnet-to-iptables.md)"
---

# ip-masq-agent

Kubernetes 节点上管理 Pod 访问外网时 SNAT（源地址转换）行为的组件。默认情况下，Pod 访问外网会被 MASQUERADE，但集群内网段不做伪装。

## 默认不伪装的网段

- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

## 自定义配置

当集群内存在自定义内网段（如专有云网段、VPN 网段）时，需使用 ip-masq-agent 配置避免内网流量被误 SNAT。通过 ConfigMap 指定非伪装网段列表，agent 会动态更新节点上的 iptables 规则。

## 相关

- [kube-proxy iptables 模式](kube-proxy%20iptables%20模式_kube-proxy%20iptables模式.md)
- [Kubernetes 网络子网](Kubernetes%20网络子网_Kubernetes网络子网.md)
- [内核网络参数](内核网络参数_内核网络参数.md)
