---
title: JVM 调优
type: concept
tags: [java, jvm, performance, troubleshooting]
aliases:
  - JVM Tuning
  - JVM调优
sources: []
---

# JVM 调优 (JVM Tuning)

JVM 调优是指通过调整 JVM 启动参数、选择合适的垃圾收集器（GC），以及利用诊断工具排查内存或 CPU 问题，从而让运行在 [JVM 运行时数据区](JVM%20Run-Time%20Data%20Areas_JVM%20运行时数据区.md) 的 Java 应用达到吞吐量、延迟和内存占用的最佳平衡。

## 调优核心目标

通常无法同时完美满足以下三个目标，需要根据业务场景（CAP 理论的变种）进行权衡：
1. **吞吐量 (Throughput)**：运行用户代码时间 / (运行用户代码时间 + 垃圾收集时间)。高吞吐量适合后台批处理计算任务。
2. **停顿时间 (Pause Time)**：垃圾收集时导致应用线程挂起（Stop The World, STW）的时间。低延迟适合对响应时间敏感的 Web API 和在线交易系统。
3. **内存占用 (Footprint)**：应用运行所需的物理内存大小。

## 核心 JVM 参数

- **`-Xms` / `-Xmx`**：设置堆的初始大小和最大大小。**最佳实践是将其设为相同的值**，避免运行时堆不断扩容和缩容带来的性能损耗。
- **`-Xmn`**：设置年轻代大小（JDK 8 及以前）。
- **`-XX:MetaspaceSize` / `-XX:MaxMetaspaceSize`**：设置元空间（方法区）的初始大小和最大大小（JDK 8 及以上）。
- **`-XX:+HeapDumpOnOutOfMemoryError`**：发生 OOM 时自动生成 Dump 文件，这是事后分析的关键。

## 垃圾收集器 (GC) 选择

不同 JDK 版本推荐的收集器有所不同：
- **JDK 8 主流**：`Parallel Scavenge` + `Parallel Old`（主打吞吐量），或者 `ParNew` + `CMS`（主打低延迟）。
- **JDK 9 - 14 主流**：**G1 (Garbage-First)**。兼顾高吞吐量与可预测的低延迟，适合大内存（多核、4GB以上堆）环境。
- **JDK 15 及以上前沿**：**ZGC** 或 **Shenandoah**。致力于将停顿时间控制在 10ms 乃至亚毫秒级别，即使堆内存达到 TB 级别。

## 常用诊断与调优工具

1. **JDK 自带命令行工具**：
   - `jps`：查看虚拟机进程及基本的类装载、GC 统计信息。
   - `jstat`：监视堆内存各个区域的使用率和 GC 频率。
   - `jmap`：生成堆快照（Heap Dump），也可查看内存实例分布。
   - `jcmd`：多功能诊断工具（JDK 8+ 推荐）。
   - `jstack`：生成线程快照（Thread Dump），排查死锁、CPU 100% 死循环问题。
2. **可视化与第三方工具**：
   - **Arthas**（阿里开源）：线上实时诊断利器，无需重启即可追踪方法耗时、反编译代码。
   - **MAT (Memory Analyzer Tool)**：分析庞大 Heap Dump 文件的神器，找出内存泄漏（Memory Leak）的根源对象（如深挖 GC Root 引用链）。
   - **VisualVM** / **JConsole**。

## 相关链接
- 内存结构基础：[JVM 运行时数据区](JVM%20Run-Time%20Data%20Areas_JVM%20运行时数据区.md)