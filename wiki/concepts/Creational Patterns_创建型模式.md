---
title: Creational Patterns（创建型模式）
type: concept
tags: [java, design-patterns, gof, creational]
sources:
  - "[2026-05-22-java-design-patterns-liaoxuefeng](raw/clippings/2026-05-22-java-design-patterns-liaoxuefeng.md)"
---

# Creational Patterns（创建型模式）

创建型模式是 [GoF 设计模式](GoF%20Design%20Patterns_GoF设计模式.md) 的第一大类，关注**如何创建对象**。其核心思想是将对象的**创建与使用相分离**，使两者能相对独立地变换。

## 五种模式

- **工厂方法（Factory Method）**：定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类。
- **抽象工厂（Abstract Factory）**：提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。
- **建造者（Builder）**：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。
- **原型（Prototype）**：用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。
- **单例（Singleton）**：保证一个类仅有一个实例，并提供一个访问它的全局访问点。构造方法必须为 `private`，通过静态方法返回唯一实例。Java 标准库中的 `Runtime` 类就是单例。

## 关键原则

创建型模式遵循**开闭原则**——对扩展开放，对修改关闭。增加新产品类时，通过扩展工厂或建造者即可，无需修改已有调用代码。

## Related

- [GoF 设计模式](GoF%20Design%20Patterns_GoF设计模式.md) — 设计模式总览
- [结构型模式](Structural%20Patterns_结构型模式.md) — 对象的组合与结构
- [行为型模式](Behavioral%20Patterns_行为型模式.md) — 对象的协作与职责分配
