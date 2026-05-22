---
title: Volatile 关键字
type: concept
tags: [java, concurrency, jmm]
aliases:
  - Volatile
  - volatile
sources: []
---

# Volatile 关键字

`volatile` 是 Java 提供的一种轻量级的同步机制。在 [JMM](Java%20Memory%20Model_Java%20内存模型.md) 的规范下，它主要用来解决多线程环境下的变量**可见性**和**有序性**问题。

## 核心特性

### 1. 保证可见性 (Visibility)
当一个变量被声明为 `volatile` 时，它保证了新值能立即刷新到主内存 (Main Memory) 中，并且其他线程每次读取该变量前，必须强制从主内存中刷新最新值，而不是从线程的私有工作内存中读取。
- **底层原理**：通过缓存一致性协议（如 Intel 的 MESI）和总线嗅探机制实现。

### 2. 禁止指令重排序 (Ordering)
编译器和处理器为了优化性能，往往会对指令进行重排序。`volatile` 变量读写前后的指令不会跨过它进行重排序。
- **底层原理**：通过在汇编指令层面插入**内存屏障 (Memory Barrier)** 来实现。写操作后插入 StoreLoad 屏障，读操作前插入 LoadLoad 屏障。

### 3. 不保证原子性 (No Atomicity)
`volatile` 只能保证单次读/写的原子性。对于复合操作（如 `i++`），`volatile` 无法保证其在多线程下的安全。如果需要原子性复合操作，应使用 [Synchronized](Synchronized_Synchronized关键字.md) 或 `java.util.concurrent.atomic` 包下的原子类。

## 典型应用场景

1. **状态标志位 (Status Flag)**
   用于多线程间传递状态信号，例如作为一个循环的终止标志。
   ```java
   volatile boolean shutdownRequested = false;
   ```
2. **单例模式的双重检查锁 (Double-Checked Locking)**
   防止对象在初始化过程中发生指令重排，导致其他线程获取到一个未完全初始化的“半成品”对象。
   ```java
   public class Singleton {
       // 必须加 volatile 禁止指令重排
       private static volatile Singleton instance;
       
       public static Singleton getInstance() {
           if (instance == null) {
               synchronized (Singleton.class) {
                   if (instance == null) {
                       instance = new Singleton(); // 这里不是原子操作
                   }
               }
           }
           return instance;
       }
   }
   ```

## 相关链接
- 理论基础：[Java 内存模型](Java%20Memory%20Model_Java%20内存模型.md)
- 锁机制：[Synchronized_Synchronized关键字](Synchronized_Synchronized关键字.md)