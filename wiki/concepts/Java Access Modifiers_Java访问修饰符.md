---
title: Java Access Modifiers
type: concept
tags: [java, oop, encapsulation, access-control]
sources:
  - "[2026-05-28-java-access-modifiers](raw/clippings/2026-05-28-java-access-modifiers.md)"
---

# Java 访问修饰符

Java 访问修饰符用于控制类、字段、构造函数和方法的可见性，是实现封装（Encapsulation）的核心机制。

## 四种访问修饰符

| 修饰符 | 可见范围 | 适用对象 |
|--------|----------|----------|
| **private** | 仅当前类 | 字段、方法、构造函数、内部类 |
| **default** | 同包内 | 类、字段、方法、构造函数 |
| **protected** | 同包内 + 子类 | 字段、方法、构造函数、内部类 |
| **public** | 全部 | 类、字段、方法、构造函数 |

## 访问级别对比

```
┌─────────────────────────────────────────────────────────────────┐
│                      访问范围                                  │
├─────────────┬─────────────┬─────────────┬─────────────────────┤
│ 修饰符      │ 同类        │ 同包        │ 子类        │ 其他   │
├─────────────┼─────────────┼─────────────┼─────────────┼────────┤
│ private     │ ✓           │ ✗           │ ✗           │ ✗      │
│ default     │ ✓           │ ✓           │ ✗           │ ✗      │
│ protected   │ ✓           │ ✓           │ ✓           │ ✗      │
│ public      │ ✓           │ ✓           │ ✓           │ ✓      │
└─────────────┴─────────────┴─────────────┴─────────────┴────────┘
```

## private - 封装内部实现

仅类内部可访问，用于隐藏实现细节。

```java
public class User {
    private String password;  // 私有字段
    
    public String getPasswordHash() {
        return hash(password);  // 通过方法访问
    }
}
```

**典型用途**：
- 封装敏感数据（如密码）
- 内部辅助方法
- 私有构造函数（单例模式、工厂模式）

## default - 包级访问

不显式声明修饰符时的默认行为，同包内可访问。

```java
// package: com.example.service
class InternalService {
    void process() { /* ... */ }  // default access
}
```

**典型用途**：
- 包内协作的类
- 不对外暴露的内部组件

## protected - 支持继承

同包内可访问 + 子类可访问（跨包也可）。

```java
public class BaseService {
    protected Logger logger = LoggerFactory.getLogger(getClass());
}

public class UserService extends BaseService {
    public void doSomething() {
        logger.info("doing something");  // 可访问父类 protected 字段
    }
}
```

**典型用途**：
- 父类中需要被子类访问的字段/方法
- 模板方法模式中的钩子方法

## public - 对外暴露接口

全部可访问，用于定义公共 API。

```java
public class UserController {
    public User getUserById(Long id) {
        return userRepository.findById(id);
    }
}
```

**典型用途**：
- 对外暴露的服务接口
- API 入口方法
- 公共数据模型

## 特殊规则

### 类的访问修饰符

| 修饰符 | 顶层类 | 内部类 |
|--------|--------|--------|
| private | ❌ | ✓ |
| default | ✓ | ✓ |
| protected | ❌ | ✓ |
| public | ✓ | ✓ |

### 接口的访问修饰符

- 接口中的字段默认是 `public static final`
- 接口中的方法默认是 `public abstract`
- 不能使用 `private`、`protected` 或 default

### 继承与访问修饰符

**规则**：子类重写方法时，访问级别**不能降低**

```java
public class Parent {
    protected void doSomething() { }
}

public class Child extends Parent {
    // 正确：protected → public（允许扩大）
    @Override
    public void doSomething() { }
    
    // 错误：protected → private（不允许缩小）
    // @Override
    // private void doSomething() { }
}
```

## 设计原则

| 原则 | 说明 |
|------|------|
| **最小权限原则** | 尽可能使用最小的访问级别 |
| **封装隐藏** | 使用 private 隐藏内部实现 |
| **接口隔离** | 使用 public 只暴露必要的 API |

## 最佳实践

1. **字段**：默认使用 `private`，通过 getter/setter 访问
2. **方法**：仅暴露必要的 `public` 方法
3. **内部类**：使用 `private` 或 `protected`
4. **包内类**：使用 `default` 访问级别

## 相关

- [Java Programming Language](Java%20Programming%20Language_Java编程语言.md)
- [SOLID Principles](SOLID%20Principles_SOLID原则.md) — 封装原则
- [Java Naming Conventions](Java%20Naming%20Conventions_Java命名规范.md)