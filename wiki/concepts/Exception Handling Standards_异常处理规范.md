---
title: Exception Handling Standards（异常处理规范）
type: concept
tags: [java, exception-handling, coding-standards, alibaba]
sources:
  - "[2026-05-26-alibaba-java-development-manual](raw/clippings/2026-05-26-alibaba-java-development-manual.md)"
---

# Exception Handling Standards（异常处理规范）

异常处理规范是Java开发中的重要实践，良好的异常处理能够提升代码健壮性、可维护性，并帮助快速定位问题。

## 核心原则

### 1. 预检查优于异常捕获

**强制**：可以通过预检查方式规避的 RuntimeException 不应该通过 catch 方式处理。

| 应该预检查 | 不应该捕获 |
|-----------|-----------|
| `if (obj != null)` | `catch (NullPointerException)` |
| `if (str != null && str.isEmpty())` | `catch (NumberFormatException)` |

### 2. 异常不用做流程控制

**强制**：异常不要用来做流程控制、条件控制。异常的处理效率比条件判断低很多。

### 3. 区分稳定代码与非稳定代码

**强制**：catch 时必须分清异常类型，进行针对性处理。对大段代码进行 try-catch 是不负责任的表现。

### 4. 异常必须处理或抛出

**强制**：捕获异常是为了处理它，不要捕获后什么都不处理就抛弃。如果不想处理，请抛给调用者。

### 5. finally 块规范

| 规则 | 说明 |
|------|------|
| **必须关闭资源** | finally 块必须对资源对象、流对象进行关闭 |
| **禁止 return** | 不要在 finally 块中使用 return，会覆盖 try 中的 return |

### 6. 异常匹配规则

**强制**：捕获异常与抛异常必须是完全匹配，或者捕获异常是抛异常的父类。

## NPE 防护指南

防止 NPE（NullPointerException）是程序员的基本修养。NPE 产生的典型场景：

| 场景 | 防护措施 |
|------|---------|
| 基本类型自动拆箱 | 返回类型为基础类型时，return 包装数据类型对象要防 NPE |
| 数据库查询结果 | 可能为 null，需要空指针判断 |
| 集合元素 | isNotEmpty 也可能取出 null 元素 |
| 远程调用返回对象 | 一律要求进行空指针判断 |
| 级联调用 | `obj.getA().getB().getC()` 易产生 NPE，推荐使用 `Optional` |

## 异常分类建议

| 类型 | 使用建议 |
|------|---------|
| **Checked Exception** | 业务预知范围内的异常，调用方必须处理 |
| **Unchecked Exception** | RuntimeException 子类，无需强制捕获 |
| **自定义异常** | 使用有业务含义的自定义异常，如 `DAOException`、`ServiceException` |
| **禁止** | 直接抛出 `new RuntimeException()` 或 `Exception`、`Throwable` |

## 跨应用异常处理

| 场景 | 推荐方式 |
|------|---------|
| **公司外 HTTP/API 开放接口** | 使用错误码 |
| **应用内部** | 推荐异常抛出 |
| **跨应用 RPC 调用** | 优先使用 Result 方式，封装 `isSuccess()` 方法、错误码、错误信息 |

## DRY 原则

**参考**：避免重复代码（Don't Repeat Yourself）。必要时抽取共性方法，或者抽象公共类。

## Related

- [Code Quality Standards_代码质量规范](Code%20Quality%20Standards_代码质量规范.md)
- [Java Naming Conventions_Java命名规范](Java%20Naming%20Conventions_Java命名规范.md)
- [Volatile_Volatile关键字](Volatile_Volatile关键字.md)
