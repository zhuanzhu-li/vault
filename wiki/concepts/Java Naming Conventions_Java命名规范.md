---
title: Java Naming Conventions（Java命名规范）
type: concept
tags: [java, naming-conventions, coding-standards, alibaba]
sources:
  - "[2026-05-26-alibaba-java-development-manual](raw/clippings/2026-05-26-alibaba-java-development-manual.md)"
---

# Java Naming Conventions（Java命名规范）

Java命名规范是阿里巴巴Java开发手册中的核心规约之一，通过统一的命名风格提升代码可读性和团队协作效率。

## 核心规约

### 命名风格

| 元素 | 规范 | 正例 | 反例 |
|------|------|------|------|
| **类名** | UpperCamelCase | `MarcoPolo`、`UserDO`、`XmlService` | `macroPolo`、`UserDo` |
| **方法名/参数名/变量** | lowerCamelCase | `localValue`、`getHttpMessage()` | — |
| **常量** | 全大写+下划线 | `MAX_STOCK_COUNT` | `MAX_COUNT` |
| **包名** | 全小写，单数形式 | `com.alibaba.ai.util` | — |

### 特殊命名规则

#### 1. POJO 类布尔类型
- **强制**：POJO类中布尔类型的变量都不要加 is 前缀
- **原因**：部分框架解析可能引起序列化错误
- **反例**：`Boolean isDeleted` 的方法也是 `isDeleted()`，RPC反向解析时会误以为属性名是 `deleted`

#### 2. 数组定义
- **强制**：类型与中括号紧挨相连
- **正例**：`int[] arrayDemo`
- **反例**：`String args[]`

#### 3. 抽象类/异常类/测试类
- **抽象类**：`Abstract` 或 `Base` 开头
- **异常类**：`Exception` 结尾
- **测试类**：以要测试的类名开始，`Test` 结尾

### 接口与实现类

| 场景 | 规范 | 正例 |
|------|------|------|
| Service/DAO 暴露接口 | 接口名 + `Impl` 后缀 | `CacheServiceImpl` 实现 `CacheService` |
| 形容能力的接口 | `-able` 形式 | `Translatable` |

### 领域模型命名

| 类型 | 格式 | 说明 |
|------|------|------|
| 数据对象 | `xxxDO` | xxx为数据表名 |
| 数据传输对象 | `xxxDTO` | 业务领域相关 |
| 展示对象 | `xxxVO` | 一般为网页名称 |
| 统称 | POJO | 禁止命名成 `xxxPOJO` |

### Service/DAO 层方法命名

| 操作 | 前缀 |
|------|------|
| 获取单个对象 | `get` |
| 获取多个对象 | `list` |
| 获取统计值 | `count` |
| 插入 | `save` / `insert` |
| 删除 | `remove` / `delete` |
| 修改 | `update` |

## Related

- [Code Quality Standards_代码质量规范](Code%20Quality%20Standards_代码质量规范.md)
- [Java Collection Framework_Java集合框架](Java%20Collection%20Framework_Java集合框架.md)
