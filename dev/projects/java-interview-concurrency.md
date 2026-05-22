---
title: Java 面试 - 并发编程与多线程
type: project
tags: [interview, java, concurrency, juc]
status: in-progress
sources: []
parent: "[[Java 面试复习计划]]"
---

# 并发编程与多线程 (JUC)

> **[AI 协作指令]** 见：[[Java 面试复习计划#AI 协作指令]]

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
