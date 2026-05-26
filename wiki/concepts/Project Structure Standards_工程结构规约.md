---
title: Project Structure Standards（工程结构规约）
type: concept
tags: [java, project-structure, coding-standards, alibaba]
sources:
  - "[2026-05-26-alibaba-java-development-manual](raw/clippings/2026-05-26-alibaba-java-development-manual.md)"
---

# Project Structure Standards（工程结构规约）

工程结构规约定义了Java应用的目录组织、二方库依赖管理和服务器配置规范，旨在提升项目的可维护性和模块化程度。

## 一、应用分层

### 经典分层架构

| 层级 | 职责 | 示例包名 |
|------|------|---------|
| **Facade / Controller** | 统一对外接口，参数校验 | `com.alibaba.app.facade` |
| **Service / Business** | 业务逻辑处理，事务管理 | `com.alibaba.app.service` |
| **Repository / DAO** | 数据访问层，与数据库交互 | `com.alibaba.app.dal` |
| **Model / Entity** | 数据模型，与数据库表对应 | `com.alibaba.app.model` |

### 分层原则

| 原则 | 说明 |
|------|------|
| **禁止越层调用** | 跳过 Service 直接调用 DAO |
| **禁止反向调用** | 下层不应调用上层接口 |
| **面向接口编程** | Service 对外暴露接口，DAO 使用接口+实现 |

## 二、二方库依赖

### 依赖原则

| 原则 | 说明 |
|------|------|
| **精简依赖** | 只引入必要的依赖，避免传递依赖膨胀 |
| **版本统一管理** | 相同框架使用相同版本 |
| **禁止循环依赖** | 模块间不应存在循环调用 |

### 依赖范围

| 范围 | 用途 |
|------|------|
| `compile` | 编译、运行时都依赖 |
| `provided` | 编译时依赖，运行时由容器提供 |
| `test` | 仅测试时使用 |

## 三、服务器配置

### JVM 参数建议

| 参数类别 | 建议配置 |
|---------|---------|
| 堆内存 | `-Xms` = `-Xmx`，避免动态扩展 |
| GC | 生产环境使用 G1 或 ZGC |
| 日志 | `-XX:+HeapDumpOnOutOfMemoryError` |
| 字符集 | `-Dfile.encoding=UTF-8` |

### 应用配置

| 项目 | 建议 |
|------|------|
| 时区 | 统一使用 UTC 或指定时区 |
| 序列化 | 统一 JSON 序列化框架 |
| 日志目录 | 配置到独立磁盘分区 |

## 四、工程结构示例

```
project/
├── src/main/java/
│   └── com/alibaba/app/
│       ├── facade/          # 对外接口层
│       ├── service/         # 业务逻辑层
│       ├── dal/             # 数据访问层
│       ├── model/           # 数据模型
│       ├── config/          # 配置类
│       └── util/            # 工具类
├── src/main/resources/
│   ├── application.yml
│   └── mapper/              # MyBatis 映射文件
├── src/test/java/
└── pom.xml
```

## Related

- [Code Quality Standards_代码质量规范](Code%20Quality%20Standards_代码质量规范.md)
- [Java Naming Conventions_Java命名规范](Java%20Naming%20Conventions_Java命名规范.md)
- [Spring Framework](Spring%20Framework.md)
