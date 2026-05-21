---
title: Synchronized 关键字
type: concept
tags: [java, concurrency, jmm, lock]
aliases:
  - Synchronized
  - synchronized
sources: []
---

# Synchronized 关键字

`synchronized` 是 Java 中的一个内置关键字，提供了一种**悲观锁（排他锁）**的机制。在多线程并发场景下，它能够保证被它修饰的方法或代码块在任意时刻只能被一个线程执行。

## 在 JMM 下提供的并发保证

在 [[Java Memory Model_Java 内存模型|JMM]] 的规范下，`synchronized` 提供了两项核心保证：
1. **原子性 (Atomicity)**：确保同步块内的操作被视为一个不可分割的整体，不会被其他线程中断。
2. **可见性 (Visibility)**：根据 JMM 的管程锁定规则（Monitor Lock Rule），对一个锁的解锁（unlock）先行发生于（happens-before）随后对同一个锁的加锁（lock）。这意味着一个线程在释放锁之前对共享变量所做的修改，对随后获取同一把锁的线程是立即可见的。

## 使用方式与锁对象

`synchronized` 可以修饰不同的目标，其本质都是在**某一个对象的监视器（Monitor）**上加锁：

1. **修饰实例方法**：锁是当前实例对象（`this`）。
   ```java
   public synchronized void method() { ... }
   ```
2. **修饰静态方法**：锁是当前类的 Class 对象（`Class.class`）。由于 Class 对象全局唯一，因此所有调用该静态同步方法的线程都会互斥。
   ```java
   public static synchronized void staticMethod() { ... }
   ```
3. **修饰代码块**：锁是括号中配置的对象。这种方式更加灵活，可以减小锁的粒度，提升并发性能。
   ```java
   public void blockMethod() {
       synchronized(this) { // 或 synchronized(Object.class)
           // 临界区代码
       }
   }
   ```

## 锁的底层实现与升级机制

在 JDK 1.6 之前，`synchronized` 依赖于操作系统的 Mutex Lock 实现，导致线程在用户态和内核态之间切换，性能开销极大，被称为**重量级锁**。

JDK 1.6 引入了大量的优化机制（如锁消除、锁粗化），最核心的是**锁升级（Lock Escalation）机制**。锁的状态保存在对象的 **对象头（Mark Word）** 中，根据竞争情况会自动升级，且**不可降级**：

1. **无锁 (Unlocked)**：初始状态。
2. **偏向锁 (Biased Lock)**：在绝大多数情况下，不仅不存在多线程竞争，而且总是由同一线程多次获得锁。偏向锁会将持有锁的线程 ID 记录在对象头中。此后该线程再次进入和退出同步块时，不需要进行任何 CAS 或加锁操作。
3. **轻量级锁 (Lightweight Lock)**：当有另一个线程尝试竞争该偏向锁时，偏向锁升级为轻量级锁。此时竞争的线程不会立刻阻塞挂起，而是通过**自旋（Spinning/CAS）** 的方式尝试获取锁，期望持有锁的线程很快释放。
4. **重量级锁 (Heavyweight Lock)**：如果自旋多次（自适应自旋）仍然获取不到锁，或者有超过两个以上的线程竞争同一个锁，轻量级锁就会膨胀为重量级锁。此时，未能获取锁的线程会被真正挂起进入阻塞队列，交由操作系统调度。

## 与 Volatile 的对比

- [[Volatile_Volatile关键字|Volatile]]：是一种极轻量的同步机制。只能保证变量的**可见性**和**有序性**，**不保证原子性**。不会引起线程上下文切换。
- `synchronized`：是重量级（尽管有锁升级优化）的同步机制。能同时保证**原子性**、**可见性**和**有序性**。竞争激烈时会导致线程挂起。

## 相关链接
- 理论基础：[[Java Memory Model_Java 内存模型]]
- 另一种可见性机制：[[Volatile_Volatile关键字]]