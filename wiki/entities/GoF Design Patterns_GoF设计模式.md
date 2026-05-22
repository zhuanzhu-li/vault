---
title: GoF Design Patterns（GoF 设计模式）
type: entity
tags: [java, design-patterns, gof, oop]
sources:
  - "[[raw/clippings/2026-05-22-java-design-patterns-liaoxuefeng]]"
---

# GoF Design Patterns（GoF 设计模式）

GoF（Gang of Four，四人帮）设计模式是由 Erich Gamma、Richard Helm、Ralph Johnson 和 John Vlissides 在 1994 年合著的《Design Patterns: Elements of Reusable Object-Oriented Software》一书中总结的 23 种经典面向对象设计模式。

设计模式的核心目的是**可重用代码，提高代码的可扩展性和可维护性**，基于 OOP 编程提炼，遵循开闭原则和里氏替换原则。

## 三大分类

- **[[Creational Patterns_创建型模式|创建型模式]]（5 种）**：将对象的创建与使用分离——工厂方法、抽象工厂、建造者、原型、单例
- **[[Structural Patterns_结构型模式|结构型模式]]（7 种）**：通过组合与动态组合获得更灵活的对象结构——适配器、桥接、组合、装饰器、外观、享元、代理
- **[[Behavioral Patterns_行为型模式|行为型模式]]（11 种）**：算法与对象间的职责分配和协作——责任链、命令、解释器、迭代器、中介、备忘录、观察者、状态、策略、模板方法、访问者

## 设计原则

- **开闭原则（OCP）**：对扩展开放，对修改关闭
- **里氏替换原则（LSP）**：子类应能替换父类且行为不变

## Related

- [[Creational Patterns_创建型模式|创建型模式]]
- [[Structural Patterns_结构型模式|结构型模式]]
- [[Behavioral Patterns_行为型模式|行为型模式]]
- [[Design Principles_设计原则|设计原则]]
