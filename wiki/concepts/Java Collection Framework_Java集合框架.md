---
title: Java 集合框架 (Java Collection Framework)
type: concept
tags: [java, collections, data-structures]
sources: []
---

# Java 集合框架 (Java Collection Framework)

Java 集合框架提供了一套性能优良、使用方便的接口和类，用于存储和处理数据集合。

## 核心接口
集合框架主要分为两大派系：`Collection` 和 `Map`。

### 1. Collection 接口
用于存储单一元素，主要有三个子接口：

- **List**：有序、可重复。主要实现类有 [ArrayList](ArrayList_ArrayList.md)、`LinkedList`、`Vector`。
- **Set**：无序、不可重复。主要实现类有 `HashSet`、`LinkedHashSet`、`TreeSet`。
- **Queue**：队列接口，按特定规则（如FIFO）确定元素的顺序。主要实现类有 `PriorityQueue`、`ArrayDeque`。


### 2. Map 接口
用于存储键值对（Key-Value），Key 不可重复，Value 可重复。

主要实现类有 [HashMap](HashMap_HashMap.md)、`LinkedHashMap`、`TreeMap`、[ConcurrentHashMap](ConcurrentHashMap_ConcurrentHashMap.md)、`Hashtable`。


## 常见面试考点
- 各个实现类的底层数据结构是什么？
- 线程安全与非线程安全的集合有哪些？
- 快速失败 (fail-fast) 与安全失败 (fail-safe) 机制的区别？
