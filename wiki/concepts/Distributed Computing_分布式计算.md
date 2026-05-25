---
title: 分布式计算
type: concept
tags: [distributed-systems, computing, distributed-computing]
aliases:
  - Distributed Computing
  - 分布式计算
  - 分布式系统
sources:
  - "[2026-05-25-hbase-reference-guide-preface](raw/clippings/2026-05-25-hbase-reference-guide-preface.md)"
---

# 分布式计算

分布式系统涉及多台计算机协同工作，对用户呈现出单一系统的视图。这是一个兼具挑战与魅力的领域。

## 分布式计算的谬误

[分布式计算的谬误](http://en.wikipedia.org/wiki/Fallacies_of_Distributed_Computing)（Fallacies of Distributed Computing）是分布式系统设计中常被忽略的一组假设，常被视作分布式计算入门的第一课：

1. **网络是可靠的**——实际上网络可能发生分区、丢包、延迟
2. **延迟为零**——远程调用比本地调用慢数个数量级
3. **带宽是无限的**——网络带宽存在物理上限
4. **网络是安全的**——分布式系统暴露了更多攻击面
5. **拓扑结构不会变**——节点加入、退出、故障是常态
6. **只有一个管理员**——大型分布式系统由多个团队/组织运维
7. **传输成本为零**——序列化、反序列化、协议开销不可忽视
8. **网络是同构的**——异构环境中的协议兼容性是需要处理的问题

## 分布式系统的复杂性

从 [Apache HBase](Apache%20HBase_Apache%20HBase.md) 的运营经验可见，分布式集群的故障来源极广：
- **软件层面**：应用本身的 Bug、配置错误
- **操作系统层面**：内核参数、文件系统配置不当
- **硬件层面**：网卡驱动程序故障、内存总线带宽不足

## 相关

- [Apache HBase](Apache%20HBase_Apache%20HBase.md)
- [支持与测试预期](Support%20&%20Testing%20Expectations_支持与测试预期.md)
