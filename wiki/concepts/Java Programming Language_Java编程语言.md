---
title: Java Programming Language
type: concept
tags: [java, programming-language, oop, jvm, enterprise]
sources:
  - "[2026-05-28-java-developer-roadmap](raw/clippings/2026-05-28-java-developer-roadmap.md)"
---

# Java 编程语言

Java 是一种跨平台、面向对象的编程语言，由 Sun Microsystems（现为 Oracle）于 1995 年发布。它以"一次编写，到处运行"（Write Once, Run Anywhere）的理念著称，是企业级应用开发的首选语言。

## 核心特点

| 特性 | 说明 |
|------|------|
| **跨平台** | 运行在 JVM 上，支持 Windows、Linux、macOS |
| **面向对象** | 完全 OOP，支持封装、继承、多态 |
| **强类型** | 编译时类型检查，减少运行时错误 |
| **自动内存管理** | 垃圾回收（GC）自动管理内存 |
| **并发支持** | 内置多线程支持，Java 21+ 引入虚拟线程 |
| **安全性** | 沙箱机制，防止恶意代码执行 |

## 学习路径

### 阶段 1：基础语法

```java
// 基本语法
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

- 数据类型（基本类型 vs 引用类型）
- 控制语句（if-else, switch, loops）
- 方法定义与调用
- 数组与集合

### 阶段 2：面向对象编程

| 概念 | 说明 |
|------|------|
| **类与对象** | 封装数据和行为 |
| **继承** | 代码复用，`extends` 关键字 |
| **多态** | 同一接口多种实现 |
| **抽象** | 隐藏实现细节 |
| **接口** | 定义契约，`interface` 关键字 |
| **记录（Record）** | Java 14+，不可变数据载体 |

### 阶段 3：高级特性

| 主题 | 关键点 |
|------|--------|
| **泛型** | 类型安全的集合操作 |
| **Lambda 表达式** | 函数式编程入门 |
| **Stream API** | 函数式数据处理 |
| **模块系统** | Java 9+，模块化编程 |
| **虚拟线程** | Java 21+，轻量级并发 |

### 阶段 4：JVM 与性能

- JVM 内存模型（堆、栈、方法区）
- 垃圾收集器（G1、ZGC、Shenandoah）
- JVM 参数调优
- JIT 编译原理

## 生态系统

### 框架

| 框架 | 用途 |
|------|------|
| **Spring Framework** | 企业级应用框架 |
| **Spring Boot** | 快速构建微服务 |
| **Quarkus** | 云原生 Java |
| **Play Framework** | 响应式 Web 开发 |

### 构建工具

| 工具 | 特点 |
|------|------|
| **Maven** | 约定优于配置 |
| **Gradle** | 灵活的构建脚本 |
| **Bazel** | 高性能多语言构建 |

### 数据库访问

| 技术 | 说明 |
|------|------|
| **JDBC** | 原生数据库连接 |
| **Hibernate** | ORM 框架 |
| **Spring Data JPA** | 数据访问抽象 |

## 应用领域

| 领域 | 应用示例 |
|------|----------|
| **企业级应用** | ERP、CRM、银行系统 |
| **Web 开发** | 电商平台、内容管理系统 |
| **桌面应用** | Eclipse、IntelliJ IDEA |
| **移动开发** | Android 应用 |
| **大数据** | Hadoop、Spark |
| **云原生** | Kubernetes 微服务 |

## 职业发展

### 初级 Java 开发工程师
- 基础语法、简单业务开发
- 使用框架进行 CRUD 操作

### 中级 Java 开发工程师
- 深入理解 JVM、并发编程
- 参与系统设计与架构讨论

### 高级/资深 Java 开发工程师
- 系统架构设计、技术选型
- 性能调优、故障排查

## 学习资源

- **官方文档**: [docs.oracle.com](https://docs.oracle.com/en/java/)
- **Roadmap**: [roadmap.sh/java](https://roadmap.sh/java)
- **书籍**: 《Java 核心技术》、《Effective Java》

## 相关

- [Spring Framework](wiki/entities/Spring%20Framework.md)
- [SOLID Principles](SOLID%20Principles_SOLID原则.md)
- [Java Naming Conventions](Java%20Naming%20Conventions_Java命名规范.md)
- [JVM Architecture](JVM%20Architecture_JVM架构.md)