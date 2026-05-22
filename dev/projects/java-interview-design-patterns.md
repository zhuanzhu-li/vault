---
title: Java 面试 - 设计模式
type: project
tags: [interview, java, design-patterns, gof]
status: in-progress
sources: []
parent: "[Java 面试复习计划](Java%20面试复习计划.md)"
---

# 设计模式 (Design Patterns)

> **[AI 协作指令]** 见：[](Java%20面试复习计划.md#AI%20协作指令)

- [ ] **设计模式基础**
  - [ ] 什么是设计模式？GoF 23 种设计模式分哪几类？为什么要使用设计模式？
    👉 核心概念总览：[GoF Design Patterns_GoF设计模式](GoF%20Design%20Patterns_GoF设计模式.md)
  - [ ] 解释开闭原则（OCP）和里氏替换原则（LSP），为什么要遵循这些原则？
    👉 核心概念总览：[](GoF%20Design%20Patterns_GoF设计模式.md#设计原则)

- [ ] **创建型模式（5 种）**
  - [ ] 单例模式有哪几种实现方式（饿汉式、懒汉式、静态内部类、枚举）？如何保证线程安全？
    👉 核心原理见：[Creational Patterns_创建型模式](Creational%20Patterns_创建型模式.md)
  - [ ] 工厂方法和抽象工厂有什么区别？各自适用什么场景？
    👉 核心原理见：[Creational Patterns_创建型模式](Creational%20Patterns_创建型模式.md)
  - [ ] 建造者模式解决了什么问题？与工厂模式有何不同？

- [ ] **结构型模式（7 种）**
  - [ ] 代理模式和装饰器模式有什么区别？动态代理是如何实现的？
    👉 核心原理见：[Structural Patterns_结构型模式](Structural%20Patterns_结构型模式.md)
  - [ ] 适配器模式在 Java 中有哪些实际应用？（如 `InputStreamReader`）
    👉 核心原理见：[Structural Patterns_结构型模式](Structural%20Patterns_结构型模式.md)
  - [ ] 外观模式在实际项目中如何应用？解决了什么问题？

- [ ] **行为型模式（11 种）**
  - [ ] 策略模式和状态模式的区别是什么？
    👉 核心原理见：[Behavioral Patterns_行为型模式](Behavioral%20Patterns_行为型模式.md)
  - [ ] 观察者模式的工作原理？Java 中有哪些内置实现？
    👉 核心原理见：[Behavioral Patterns_行为型模式](Behavioral%20Patterns_行为型模式.md)
  - [ ] 模板方法模式的核心思想？在 Spring 框架中有哪些体现？
    👉 核心原理见：[Behavioral Patterns_行为型模式](Behavioral%20Patterns_行为型模式.md)
  - [ ] 责任链模式的实际应用场景？（如 Servlet Filter、Spring Interceptor）
