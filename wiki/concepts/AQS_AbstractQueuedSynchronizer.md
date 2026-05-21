---
title: AQS (AbstractQueuedSynchronizer)
type: concept
tags: [java, juc, concurrency, lock]
sources: []
---

# AQS (AbstractQueuedSynchronizer)

AQS (AbstractQueuedSynchronizer) 是 Java 并发包 (`java.util.concurrent.locks`) 中诸多锁和同步器的核心基础框架，如 `ReentrantLock`、`CountDownLatch`、`Semaphore` 和 `ReentrantReadWriteLock` 等底层均依赖它。

## 核心运作机制

AQS 的核心思想是：**如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态；如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制 AQS 是用 CLH 队列锁实现的，即将暂时获取不到锁的线程加入到队列中。**

### 1. 核心状态：`state`
AQS 内部维护了一个 `volatile int state` 变量，用来表示同步状态。
- **独占模式**（如 `ReentrantLock`）：`state` 通常为 0 表示未锁定，1 表示已被锁定（大于 1 表示重入）。
- **共享模式**（如 `Semaphore`）：`state` 可以表示剩余的许可证数量。
- 状态的修改：AQS 提供了 `getState()`、`setState()` 和 `compareAndSetState()` (CAS 操作) 来保证状态修改的原子性和可见性。

### 2. CLH 同步队列 (FIFO Wait Queue)
当线程尝试获取资源失败时，AQS 会将该线程以及等待状态等信息封装成一个 Node 节点，并将其加入到内部维护的 FIFO 双向链表（变体的 CLH 队列）中。
- **入队**：线程获取锁失败后，通过自旋和 CAS 操作将自己加入到队尾，并调用 `LockSupport.park()` 阻塞自己。
- **出队**：当占用资源的线程释放锁时（调用 `release()`），会唤醒头节点的后继节点，后继节点被唤醒后重新尝试获取资源。

### 3. 两种资源共享方式
- **Exclusive（独占）**：只有一个线程能执行，如 `ReentrantLock`。其中又细分为公平锁和非公平锁。
- **Share（共享）**：多个线程可同时执行，如 `Semaphore`、`CountDownLatch`。

## AQS 设计模式：模板方法模式

AQS 使用了**模板方法设计模式**。自定义同步器不需要实现所有细节，只需继承 AQS 并重写以下几个方法（负责管理资源的获取和释放逻辑即可）：
- `tryAcquire(int)`：独占方式尝试获取资源。
- `tryRelease(int)`：独占方式尝试释放资源。
- `tryAcquireShared(int)`：共享方式尝试获取资源。
- `tryReleaseShared(int)`：共享方式尝试释放资源。
- `isHeldExclusively()`：该线程是否正在独占资源。

AQS 的模板方法（如 `acquire()` 和 `release()`）会自动调用上述重写的方法，并处理所有的队列管理和线程阻塞唤醒逻辑。

## 相关链接
- 依赖于 AQS 的同步组件：`ReentrantLock`、`Semaphore`、`CountDownLatch`
- 底层实现依赖：[[Java Memory Model_Java 内存模型]]、[[Volatile_Volatile关键字]]、CAS (Compare-And-Swap)
