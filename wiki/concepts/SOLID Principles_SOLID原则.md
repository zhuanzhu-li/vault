---
title: SOLID Principles（SOLID原则）
type: concept
tags: [java, design-principles, solid, oop, software-engineering]
sources:
  - "[2026-05-26-design-principles-large-system](raw/clippings/2026-05-26-design-principles-large-system.md)"
---

# SOLID Principles（SOLID原则）

SOLID 是面向对象设计和编程的五个核心原则，由 Robert C. Martin（Uncle Bob）提出，旨在使软件更易理解、更易维护、更易扩展。

## 五大原则

### 1. 单一职责原则 (SRP)

**定义**：一个类应该只有一个改变的理由，即一个类只应该做一件事。

| 优点 | 说明 |
|------|------|
| 可维护性 | 修改一个功能只影响一个类 |
| 可测试性 | 每个类只测试一种职责 |
| 低耦合 | 类之间职责清晰，依赖少 |

**反面**：一个 `UserService` 既处理用户注册、又发送邮件、又记录日志、又生成报告。

**正面**：拆分为 `UserService`、`EmailService`、`AuditLog`、`ReportGenerator`。

### 2. 开闭原则 (OCP)

**定义**：软件实体应该对扩展开放，对修改关闭。

| 实现方式 | 示例 |
|---------|------|
| 策略模式 | 定义策略接口，新增策略不修改上下文 |
| 模板方法 | 抽象骨架，具体步骤由子类实现 |
| 装饰器模式 | 动态添加行为，不修改原类 |

### 3. 里氏替换原则 (LSP)

**定义**：子类可以替换父类，且替换后程序行为不变。

**核心要求**：
- 子类方法参数类型与父类一致或更宽
- 子类返回值类型与父类一致或更窄
- 子类不应抛出父类未声明的异常

**反面**：正方形继承矩形，`setWidth()` 和 `setHeight()` 会导致边长不等。

### 4. 接口隔离原则 (ISP)

**定义**：客户端不应该依赖它不需要的接口，即接口应该小而专一。

| 实现方式 | 说明 |
|---------|------|
| 多个专用接口 | 按角色拆分大接口 |
| 防腐层 | 为外部系统创建专用接口 |
| 组合接口 | 通过组合多个小接口实现复杂功能 |

**反面**：`IMachine` 包含 `print()`、`scan()`、`fax()`，但打印机只实现 `print()`。

**正面**：拆分为 `IPrinter`、`IScanner`、`IFax`。

### 5. 依赖倒置原则 (DIP)

**定义**：高层模块不应该依赖低层模块，两者都应该依赖抽象。

| 原则 | 说明 |
|------|------|
| 依赖抽象 | 依赖接口而非具体类 |
| 注入方式 | 构造函数注入、Setter 注入、接口注入 |
| 稳定依赖 | 依赖稳定的接口，不依赖易变的具体实现 |

## SOLID 与大型系统

| 场景 | SOLID 作用 |
|------|-----------|
| 微服务拆分 | SRP 指导服务边界划分 |
| 插件架构 | OCP 支持新功能扩展 |
| 领域驱动设计 | DIP 实现 Domain 层与 Infrastructure 解耦 |
| 单元测试 | DIP + ISP 使得 Mock 容易 |
| 代码审查 | SOLID 提供统一的评判标准 |

## 与设计模式的关系

| 设计模式 | 遵循的 SOLID 原则 |
|---------|-----------------|
| 策略模式 | OCP、DIP |
| 工厂方法 | DIP |
| 装饰器模式 | OCP、ISP |
| 代理模式 | DIP、OCP |
| 适配器模式 | ISP、DIP |

## Related

- [GoF Design Patterns_GoF设计模式](GoF%20Design%20Patterns_GoF设计模式.md)
- [Code Quality Standards_代码质量规范](Code%20Quality%20Standards_代码质量规范.md)
- [Design Patterns in Architecture_架构设计中的设计模式](Design%20Patterns%20in%20Architecture_架构设计中的设计模式.md)
