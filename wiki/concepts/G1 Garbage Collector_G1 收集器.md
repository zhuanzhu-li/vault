---
title: G1 收集器
type: concept
tags: [java, jvm, gc, garbage-collection]
aliases:
  - G1 Garbage Collector
  - G1 垃圾收集器
  - G1
sources: []
---

# G1 收集器 (Garbage-First)

G1 (Garbage-First) 是一款面向服务器的垃圾收集器，主要针对配备多颗处理器及大容量内存的机器。它从 JDK 9 开始成为默认的垃圾收集器，以极高概率满足垃圾收集停顿时间的同时，还具备高吞吐量性能特征。

## 核心设计理念

### 1. 内存布局：化整为零 (Region)
传统的 GC（如 CMS、Parallel）将堆内存在物理上划分为年轻代、老年代。而 G1 把连续的 Java 堆划分为多个大小相等的**独立区域 (Region)**。
- 此时的年轻代和老年代不再是物理隔离的，它们都是一部分 Region 的集合。
- 此外还有一种特殊的 **Humongous** 区域，专门用来存储大对象（大小超过 Region 容量一半的对象）。

### 2. 核心思想：Garbage-First (垃圾优先)
G1 跟踪各个 Region 里面垃圾堆积的价值大小（回收所获得的空间大小以及回收所需时间的经验值），在后台维护一个优先级列表。
- 每次根据用户设定的**允许的收集停顿时间**（`-XX:MaxGCPauseMillis`，默认 200ms），优先回收价值收益最大的那些 Region，这就是 "Garbage-First" 名字的由来。

## 工作过程 (主要指 Mixed GC)

G1 的收集过程大致分为以下四个步骤：
1. **初始标记 (Initial Marking)**：仅仅只是标记一下 GC Roots 能直接关联到的对象。这个阶段**需要停顿线程 (STW)**，但耗时很短。
2. **并发标记 (Concurrent Marking)**：从 GC Root 开始对堆中对象进行可达性分析，递归扫描整个堆里的对象图，找出要回收的对象。这阶段**与用户线程并发执行**。
3. **最终标记 (Final Marking)**：对用户线程做另一个短暂的暂停，用于处理并发标记阶段因用户程序继续运作而导致引用产生变动的那一部分记录（采用 SATB 算法）。**需要停顿线程 (STW)**。
4. **筛选回收 (Live Data Counting and Evacuation)**：负责更新 Region 的统计数据，对各个 Region 的回收价值和成本进行排序，根据用户所期望的停顿时间来制定回收计划，然后把决定回收的那一部分 Region 的存活对象复制到空的 Region 中，再清理掉整个旧 Region 的全部空间。**需要停顿线程 (STW)**。

## 与 CMS 收集器的核心区别

| 特性 | CMS (Concurrent Mark Sweep) | G1 (Garbage-First) |
| :--- | :--- | :--- |
| **内存划分** | 物理分代（连续的年轻代/老年代） | 逻辑分代，物理上划分为 Region |
| **基础算法** | 标记-清除 (Mark-Sweep)，容易产生空间碎片 | 整体看是标记-整理，局部看是复制算法，**不会产生内存空间碎片** |
| **停顿时间** | 不可预测 | 可预测（用户可设定目标停顿时间 `-XX:MaxGCPauseMillis`） |
| **并发标记算法** | 增量更新 (Incremental Update) | SATB (Snapshot At The Beginning) |
| **适用场景** | 中等规模内存（通常 4GB 以内） | 大容量内存（8GB 以上更显优势）、多核处理器 |

## 相关链接
- JVM 调优与参数：[JVM Tuning_JVM调优](JVM%20Tuning_JVM调优.md)
- 内存物理区域：[JVM Run-Time Data Areas_JVM 运行时数据区](JVM%20Run-Time%20Data%20Areas_JVM%20运行时数据区.md)