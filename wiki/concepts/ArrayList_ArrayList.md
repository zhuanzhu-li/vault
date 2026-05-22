---
title: ArrayList
type: concept
tags: [java, collections, list]
sources: []
---

# ArrayList


`ArrayList` 是 [[Java Collection Framework_Java集合框架|Java 集合框架]]中 `List` 接口的主要实现类之一，底层基于**动态数组**实现。


## 核心特性
- **数据结构**：底层是一个 `Object[]` 数组。
- **特点**：查询快（支持随机访问，时间复杂度 $O(1)$），增删慢（需要移动元素，时间复杂度 $O(n)$）。
- **线程安全性**：非线程安全。如果需要线程安全，可以使用 `Vector`（已淘汰）、`Collections.synchronizedList()` 或 `CopyOnWriteArrayList`。

## 扩容机制（面试重点）
1. **初始容量**：使用无参构造时，初始为一个空数组。当第一次调用 `add()` 方法时，会将其容量初始化为 10。
2. **扩容触发**：当添加元素导致数组容量不足时，触发扩容。
3. **扩容大小**：新容量通常是旧容量的 1.5 倍（`oldCapacity + (oldCapacity >> 1)`）。
4. **数据迁移**：扩容时，会使用 `Arrays.copyOf()` 将原数组的数据复制到新数组中。

## ArrayList vs LinkedList
- `LinkedList` 底层是双向链表。
- 随机访问：`ArrayList` 优于 `LinkedList`。
- 插入删除：在头部或中间插入/删除时，`LinkedList` 效率较高（但需要遍历找到位置，实际表现视情况而定）；在尾部追加时，`ArrayList` 通常更快。
- 内存占用：`LinkedList` 每个节点需要额外存储前后指针，内存开销更大。
