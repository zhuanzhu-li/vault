---
title: JVM 运行时数据区
type: concept
tags: [java, jvm, memory, architecture]
aliases:
  - JVM Run-Time Data Areas
  - JVM 运行时数据区
  - JVM 内存结构
sources: []
---

# JVM 运行时数据区 (Run-Time Data Areas)

Java 虚拟机在执行 Java 程序的过程中会把它所管理的内存划分为若干个不同的数据区域，这被称为 JVM 内存结构或运行时数据区。

> **注意区分**：这与并发模型 [[Java Memory Model_Java 内存模型|JMM]] 是两个不同维度的概念。

## 线程私有区域 (Thread Private)

这些区域的生命周期与线程相同，随线程而生，随线程而灭。

1. **程序计数器 (Program Counter Register)**
   - **作用**：当前线程所执行的字节码的行号指示器。
   - **特点**：内存空间极小，是 JVM 规范中唯一一个没有规定任何 `OutOfMemoryError` 情况的区域。
2. **Java 虚拟机栈 (JVM Stack)**
   - **作用**：描述 Java 方法执行的内存模型。每个方法在执行时都会创建一个**栈帧 (Stack Frame)**，用于存储局部变量表、操作数栈、动态链接、方法出口等信息。
   - **异常**：`StackOverflowError`（栈深度过大）和 `OutOfMemoryError`（栈扩展失败）。
3. **本地方法栈 (Native Method Stack)**
   - **作用**：与虚拟机栈类似，区别在于它是为 JVM 使用到的 Native 方法（如 C/C++ 编写的底层方法）服务的。

## 线程共享区域 (Thread Shared)

这些区域随虚拟机的启动而创建，随虚拟机的退出而销毁。

1. **Java 堆 (Heap)**
   - **作用**：存放几乎所有的**对象实例**和数组。这是 JVM 管理的内存中最大的一块。
   - **垃圾收集**：堆是垃圾收集器 (GC) 管理的主要区域。通常可以进一步划分为新生代 (Young Generation) 和老年代 (Old Generation)。
   - **异常**：`OutOfMemoryError`。
2. **方法区 (Method Area)**
   - **作用**：存储已被虚拟机加载的**类型信息、常量、静态变量、即时编译器编译后的代码缓存**等数据。
   - **演进**：在 JDK 8 之前，方法区通常由“永久代 (PermGen)”实现；在 JDK 8 及之后，改用本地内存实现的 **元空间 (Metaspace)**。
3. **运行时常量池 (Runtime Constant Pool)**
   - **作用**：方法区的一部分。用于存放编译期生成的各种字面量和符号引用。

## 直接内存 (Direct Memory)
- 并不是 JVM 运行时数据区的一部分，也不是 JVM 规范中定义的内存区域。它通过 NIO (New Input/Output) 中的 `DirectByteBuffer` 对象直接分配堆外内存，以提高性能，避免在 Java 堆和 Native 堆中来回复制数据。

## 相关链接
- 并发控制视角：[[Java Memory Model_Java 内存模型]]
- 调优实战：[[JVM Tuning]] (待完善)
- G1 GC: [[G1 Garbage Collector_G1 收集器|G1 垃圾收集器]]