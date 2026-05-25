---
title: Java 面试 - JVM 虚拟机体系
type: project
tags: [interview, java, jvm]
status: in-progress
sources: []
parent: "[Java 面试复习计划](Java%20面试复习计划.md)"
---

# JVM 虚拟机体系

> **[AI 协作指令]** 见：[](Java%20面试复习计划.md#AI%20协作指令)

- [ ] **内存划分**
  - [ ] 详细讲讲 JVM 的内存结构？哪些区域会发生 OOM？
    👉 核心原理见：[JVM Run-Time Data Areas_JVM 运行时数据区](JVM%20Run-Time%20Data%20Areas_JVM%20运行时数据区.md)
    
- [ ] **诊断与调优**
  - [ ] 线上 CPU 100% 或发生频繁 Full GC，你该如何排查？常用的工具和启动参数有哪些？
    👉 实战指南见：[JVM Tuning_JVM调优](JVM%20Tuning_JVM调优.md)

- [ ] **垃圾回收机制 (待完善)**
  - [ ] 对象什么时候可以被垃圾回收？（可达性分析）
  - [ ] 常见的垃圾回收算法有哪些？
  - [ ] 讲讲 CMS 和 G1 垃圾收集器的区别？G1 的核心原理是什么？
    👉 核心原理与对比见：[G1 Garbage Collector_G1 收集器](G1%20Garbage%20Collector_G1%20收集器.md)
