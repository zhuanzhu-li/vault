---
title: Java 面试复习计划
type: project
tags: [interview, java, career]
status: in-progress
sources: []
---

# Java 面试复习计划

> **[AI 协作指令]**
> 当用户要求总结或更新此复习计划中的知识点时，请作为 AI Agent 遵循以下标准流程：
> 1. **检索查重**：先在 `wiki/` 目录中搜索是否已存在相关知识点的卡片。
> 2. **建卡/更新**：如果已有卡片，请在原有内容上补充或提炼面试考点；如果不存在，再去 `wiki/concepts/` 新建纯粹的理论概念卡片（需包含标准 Frontmatter 和必要的双向链接 `[[Wikilinks]]`）。
> 3. **打钩挂链**：将本文件对应面试题项前方的状态由 `[ ]` 改为 `[x]`，并使用双向链接（例如 `👉 核心原理见：[[相关卡片名]]`）指向该知识卡片。
> 4. **更新索引**：若是新建了卡片，请务必前往 `wiki/Wiki Index_维基索引.md`，在相应的技术分类下增加新卡片的全局导航链接。

这是一份增量维护的 Java 面试复习地图 (Map of Content)。
核心理念：不在这里写长篇大论的答案，而是将面试题作为**索引**，指向 `wiki/` 目录中被原子化的、纯粹的底层原理卡片。

## 一、并发编程与多线程 (JUC)

- [ ] **多线程基础与并发模型**
  - [ ] 说说你对 Java 内存模型的理解？解决了什么问题？ 
    👉 核心原理见：[[Java Memory Model_Java 内存模型]]
  
- [ ] **核心关键字**
  - [ ] `volatile` 能保证原子性吗？底层是如何禁止指令重排的？ 
    👉 核心原理见：[[Volatile_Volatile关键字]]
  - [ ] 讲讲 `synchronized` 的底层原理？什么是锁升级（偏向锁/轻量级锁/重量级锁）？
    👉 核心原理见：[[Synchronized_Synchronized关键字]]

- [ ] **进阶并发工具 (待完善)**
  - [ ] `ReentrantLock` 的原理是什么？AQS 是怎么运作的？
    👉 核心原理见：[[AQS_AbstractQueuedSynchronizer]]
  - [ ] `ThreadLocal` 为什么会造成内存泄漏？如何避免？
  - [ ] 线程池的核心参数有哪些？拒绝策略有哪些？

## 二、JVM 虚拟机体系

- [ ] **内存划分**
  - [ ] 详细讲讲 JVM 的内存结构？哪些区域会发生 OOM？
    👉 核心原理见：[[JVM Run-Time Data Areas_JVM 运行时数据区]]
    
- [ ] **诊断与调优**
  - [ ] 线上 CPU 100% 或发生频繁 Full GC，你该如何排查？常用的工具和启动参数有哪些？
    👉 实战指南见：[[JVM Tuning_JVM调优]]

- [ ] **垃圾回收机制 (待完善)**
  - [ ] 对象什么时候可以被垃圾回收？（可达性分析）
  - [ ] 常见的垃圾回收算法有哪些？
  - [ ] 讲讲 CMS 和 G1 垃圾收集器的区别？G1 的核心原理是什么？
    👉 核心原理与对比见：[[G1 Garbage Collector_G1 收集器]]

## 三、Java 集合框架 (Collections)

- [x] **集合基础与 List**
  - [x] Java 集合框架有哪些核心接口？
    👉 核心原理见：[[Java Collection Framework_Java集合框架]]
  - [x] `ArrayList` 和 `LinkedList` 的区别是什么？`ArrayList` 的扩容机制是怎样的？
    👉 核心原理见：[[ArrayList_ArrayList]]

- [x] **Map 体系**
  - [x] 讲讲 `HashMap` 的底层数据结构（JDK 1.7 与 JDK 1.8 的区别）？扩容机制是怎样的？
    👉 核心原理见：[[HashMap_HashMap]]
  - [x] `HashMap` 为什么是非线程安全的？`ConcurrentHashMap` 是如何保证线程安全的？
    👉 核心原理见：[[ConcurrentHashMap_ConcurrentHashMap]]

## 四、框架与中间件 (待完善)
*等待日常学习或刷题时增量补充...*

- [ ] **Spring / Spring Boot**
- [ ] **Redis**
- [ ] **MySQL**