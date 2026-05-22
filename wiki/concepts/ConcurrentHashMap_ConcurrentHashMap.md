---
title: ConcurrentHashMap
type: concept
tags: [java, collections, concurrent, map]
sources: []
---

# ConcurrentHashMap


`ConcurrentHashMap` 是 [[Java Collection Framework_Java集合框架|Java 集合框架]]并发包 (`java.util.concurrent`) 下提供的线程安全的哈希表。


## 底层实现原理

### JDK 1.7：分段锁 (Segment)
- **数据结构**：`Segment` 数组 + `HashEntry` 数组 + 链表。
- **锁机制**：采用分段锁机制。将整个哈希表分为多个 `Segment`（默认 16 个），每个 `Segment` 继承自 `ReentrantLock`。每次操作只锁定对应的 `Segment`，不同 `Segment` 之间的操作互不影响，并发度为 16。

### JDK 1.8：CAS + Synchronized

- **数据结构**：与 JDK 1.8 的 [[HashMap_HashMap|HashMap]] 相同，即 数组 + 链表 + 红黑树。

- **锁机制**：抛弃了 `Segment` 分段锁，采用 `CAS` + `synchronized` 来保证并发安全。
- **加锁粒度**：锁的粒度更细，只锁定当前链表或红黑树的首节点（即数组的某个槽位）。只要没有哈希冲突，就不会产生锁竞争，大大提升了并发性能。

## 与 Hashtable 的对比
- `Hashtable` 使用 `synchronized` 修饰整个方法，相当于对整个哈希表加一把大锁，并发性能极差。
- `ConcurrentHashMap` 采用细粒度锁（分段锁或节点锁），在保证线程安全的同时，提供了极高的并发性能。

- `ConcurrentHashMap` 不允许 Key 或 Value 为 `null`（为了避免多线程环境下的二义性问题），而 [[HashMap_HashMap|HashMap]] 允许。

