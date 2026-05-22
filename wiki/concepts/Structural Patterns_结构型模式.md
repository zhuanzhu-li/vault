---
title: Structural Patterns（结构型模式）
type: concept
tags: [java, design-patterns, gof, structural]
sources:
  - "[[raw/clippings/2026-05-22-java-design-patterns-liaoxuefeng]]"
---

# Structural Patterns（结构型模式）

结构型模式是 [[GoF Design Patterns_GoF设计模式|GoF 设计模式]] 的第二大类，主要涉及**如何组合各种对象**以获得更好、更灵活的结构。

虽然面向对象的继承机制提供了子类扩展父类的功能，但结构型模式不仅仅使用继承，更多地通过**组合与运行期动态组合**来实现更灵活的功能。

## 七种模式

- **适配器（Adapter）**：将一个类的接口转换成客户希望的另一个接口，使得原本由于接口不兼容而不能一起工作的类可以一起工作。
- **桥接（Bridge）**：将抽象部分与它的实现部分分离，使它们都可以独立地变化。
- **组合（Composite）**：将对象组合成树形结构以表示"部分—整体"的层次结构，使得用户对单个对象和组合对象的使用具有一致性。
- **装饰器（Decorator）**：动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。
- **外观（Facade）**：为子系统中的一组接口提供一个一致的界面，定义一个高层接口使子系统更容易使用。
- **享元（Flyweight）**：运用共享技术有效地支持大量细粒度的对象。
- **代理（Proxy）**：为其他对象提供一个代理以控制对这个对象的访问。

## Related

- [[GoF Design Patterns_GoF设计模式|GoF 设计模式]] — 设计模式总览
- [[Creational Patterns_创建型模式|创建型模式]] — 对象的创建
- [[Behavioral Patterns_行为型模式|行为型模式]] — 对象的协作与职责分配
