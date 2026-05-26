---
title: Code Quality Standards（代码质量规范）
type: concept
tags: [java, code-quality, coding-standards, alibaba]
sources:
  - "[2026-05-26-alibaba-java-development-manual](raw/clippings/2026-05-26-alibaba-java-development-manual.md)"
---

# Code Quality Standards（代码质量规范）

代码质量规范是通过制定统一的编码标准来提升软件交付质量的方法论，强调"码出高效，码出质量"的核心理念。良好的代码质量规范能够减少团队协作成本、提升代码可维护性、降低线上故障风险。

## 约束力分级

| 等级 | 说明 | 示例场景 |
|------|------|---------|
| **强制** | 禁止违反，违反后必须修改 | 命名不能使用拼音、POJO类布尔变量不加is前缀 |
| **推荐** | 建议遵守，长期遵循有助于代码维护 | 使用完整的英文单词命名、自定义编程元素应完整表达语义 |
| **参考** | 了解即可，特殊场景可灵活处理 | 枚举类名带Enum后缀、各层命名规约 |

## 核心原则

### 1. 命名规范性
- 代码中的命名严禁使用拼音与英文混合的方式
- 类名使用 UpperCamelCase（如 `MarcoPolo`、`UserDO`）
- 方法名、参数名、成员变量使用 lowerCamelCase（如 `getHttpMessage`）
- 常量命名全部大写，单词间用下划线隔开（如 `MAX_STOCK_COUNT`）

### 2. 代码可读性
- 杜绝完全不规范的缩写，避免望文不知义
- 为了达到代码自解释的目标，使用尽量完整的单词组合来表达其意
- 如果模块、接口、类、方法使用了设计模式，在命名时体现出具体模式

### 3. 防御性编程
- 对任何外部输入进行有效性验证
- 预检查方式可规避的 RuntimeException 不通过 catch 方式处理
- 避免用异常做流程控制

## 价值主张

> 适当的规范和标准绝不是消灭代码内容的创造性、优雅性，而是限制过度个性化，以一种普遍认可的统一方式一起做事，提升协作效率。

## Related

- [Java Naming Conventions_Java命名规范](Java%20Naming%20Conventions_Java命名规范.md)
- [DRY Principle_DRY原则.md](DRY%20Principle_DRY原则.md)
- [SOLID Principles_SOLID原则.md](SOLID%20Principles_SOLID原则.md)
- [Alibaba_阿里巴巴](Alibaba_阿里巴巴.md)
