---
title: Java 内存模型
type: concept
tags: [java, jvm, concurrency, architecture]
aliases:
  - JMM
  - Java Memory Model
  - Java 内存模型
sources: []
---

# Java 内存模型 (Java Memory Model, JMM)

Java 内存模型（JMM）是 Java 虚拟机规范中定义的一种并发模型，主要用于屏蔽各种硬件和操作系统的内存访问差异，以实现让 Java 程序在各种平台下都能达到一致的并发内存访问效果。

> **注意区分**：JMM 经常与 [JVM 运行时数据区](JVM%20Run-Time%20Data%20Areas_JVM%20运行时数据区.md)（堆、栈、方法区等）混淆。JMM 讨论的是**多线程并发**下的可见性、有序性和原子性问题；而运行时数据区讨论的是 JVM **内存的物理划分**。

## 核心抽象结构

JMM 将内存划分为两个部分：
1. **主内存 (Main Memory)**：所有线程共享的内存区域，包含了所有的对象实例（这部分通常对应物理硬件上的内存）。
2. **工作内存 (Working Memory)**：每个线程独有的内存区域，保存了该线程使用到的变量的主内存副本拷贝。线程对变量的所有操作（读取、赋值等）都必须在工作内存中进行，而不能直接读写主内存中的数据。

## JMM 解决的三大并发问题

### 1. 可见性 (Visibility)
当一个线程修改了共享变量的值，其他线程能够立即得知这个修改。
- **解决方案**：`volatile` 关键字、`synchronized`、`final` 以及各种锁机制。`volatile` 保证了新值能立即同步到主内存，以及每次使用前立即从主内存刷新。

### 2. 原子性 (Atomicity)
一个或多个操作要么全部执行且在执行过程中不被任何因素打断，要么全部不执行。
- **解决方案**：基础数据类型读写的原子性由 JMM 保证。更大范围的原子性通过 `synchronized` 块或 `java.util.concurrent.atomic` 包下的原子类（CAS）来实现。

### 3. 有序性 (Ordering)
程序执行的顺序按照代码的先后顺序执行。在并发时，由于**指令重排序 (Instruction Reorder)**，代码的执行顺序可能与书写顺序不一致。
- **解决方案**：`volatile` 关键字（通过插入内存屏障禁止指令重排序）、`synchronized`。

## Happens-Before 原则 (先行发生原则)

这是 JMM 中非常核心的原则，用来判断数据是否存在竞争、线程是否安全：
- 如果操作 A happens-before 操作 B，那么操作 A 的产生的影响能够被操作 B 观察到（包括修改共享变量的值、发送了消息、调用了方法等）。
- **常见规则**：程序次序规则、管程锁定规则（解锁先行发生于加锁）、`volatile` 变量规则（写先行发生于读）、传递性等。

## 相关链接
- 物理内存划分：[JVM Run-Time Data Areas_JVM 运行时数据区](JVM%20Run-Time%20Data%20Areas_JVM%20运行时数据区.md)
- 并发关键字：[Volatile_Volatile关键字](Volatile_Volatile关键字.md)、[Synchronized](Synchronized_Synchronized关键字.md)