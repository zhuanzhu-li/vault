---
title: JVM Architecture
type: concept
tags: [java, jvm, architecture, virtual-machine]
aliases:
  - JVM Architecture
  - JVM架构
sources:
  - "[raw/clippings/2026-05-28-java-developer-roadmap](raw/clippings/2026-05-28-java-developer-roadmap)"
---

# JVM 架构 (JVM Architecture)

Java 虚拟机（JVM）是 Java 平台的核心组件，负责执行 Java 字节码。它实现了"一次编写，到处运行"（Write Once, Run Anywhere）的理念。

## JVM 整体架构

```
┌─────────────────────────────────────────────────────────────┐
│                    JVM 架构层次                            │
├─────────────────────────────────────────────────────────────┤
│  第1层: 类加载子系统 (Class Loader Subsystem)              │
│         ├── 启动类加载器 (Bootstrap ClassLoader)          │
│         ├── 扩展类加载器 (Extension ClassLoader)          │
│         └── 应用程序类加载器 (Application ClassLoader)    │
├─────────────────────────────────────────────────────────────┤
│  第2层: 运行时数据区 (Runtime Data Areas)                │
│         ├── 程序计数器 (PC Register)                      │
│         ├── Java 虚拟机栈 (JVM Stack)                    │
│         ├── 本地方法栈 (Native Method Stack)             │
│         ├── Java 堆 (Heap)                               │
│         └── 方法区 (Method Area / Metaspace)             │
├─────────────────────────────────────────────────────────────┤
│  第3层: 执行引擎 (Execution Engine)                      │
│         ├── 解释器 (Interpreter)                         │
│         ├── 即时编译器 (JIT Compiler)                    │
│         │     ├── C1 (Client Compiler)                   │
│         │     └── C2 (Server Compiler)                   │
│         └── 垃圾收集器 (Garbage Collector)               │
├─────────────────────────────────────────────────────────────┤
│  第4层: 本地方法接口 (JNI - Java Native Interface)       │
│         └── 本地方法库 (Native Libraries)                │
└─────────────────────────────────────────────────────────────┘
```

## 核心组件

### 1. 类加载子系统

负责将 `.class` 文件加载到 JVM 内存中，并进行链接和初始化。

| 阶段 | 说明 |
|------|------|
| **加载** | 查找并加载类的二进制数据 |
| **链接** | 验证、准备、解析符号引用 |
| **初始化** | 执行 `<clinit>` 方法，初始化静态变量 |

### 2. 运行时数据区

详见 [JVM 运行时数据区](JVM%20Run-Time%20Data%20Areas_JVM%20运行时数据区.md)

### 3. 执行引擎

负责执行字节码指令。

| 组件 | 说明 |
|------|------|
| **解释器** | 逐行解释执行字节码，启动快 |
| **JIT 编译器** | 将热点代码编译为本地机器码，执行更快 |
| **垃圾收集器** | 自动回收不再使用的对象内存 |

### 4. 本地方法接口 (JNI)

允许 Java 代码调用 Native 代码（如 C/C++）。

## JVM 工作流程

```
.java 文件
    ↓ (javac 编译)
.class 文件 (字节码)
    ↓ (类加载器)
加载到方法区
    ↓
执行引擎执行
    ↓ (JNI)
调用本地方法
```

## JVM 版本演进

| 版本 | 重要特性 |
|------|---------|
| Java 1.0 | 初始版本，经典虚拟机 |
| Java 5 | 引入泛型、注解 |
| Java 8 | Lambda、Stream API、Metaspace |
| Java 11 | LTS 版本，模块化增强 |
| Java 21 | 虚拟线程 (Virtual Threads) |

## 常见 JVM 实现

| 实现 | 说明 |
|------|------|
| **HotSpot** | Oracle/Sun 官方实现，应用最广 |
| **OpenJ9** | IBM 开发，注重内存效率 |
| **GraalVM** | 高性能多语言虚拟机 |

## 相关链接

- [JVM 运行时数据区](JVM%20Run-Time%20Data%20Areas_JVM%20运行时数据区.md)
- [JVM Tuning](JVM%20Tuning_JVM调优.md)
- [G1 Garbage Collector](G1%20Garbage%20Collector_G1%20收集器.md)
- [Java Programming Language](Java%20Programming%20Language_Java编程语言.md)
