---
title: Java 面试复习计划
type: project
tags: [interview, java, career]
status: in-progress
sources: []
---

# Java 面试复习计划

> **[AI 协作指令]**
> 当用户要求总结或更新此复习计划中的知识点时，请作为 AI Agent 遵循以下标准流程：
> 1. **检索查重**：先在 `wiki/` 目录中搜索是否已存在相关知识点的卡片。
> 2. **建卡/更新**：如果已有卡片，请在原有内容上补充或提炼面试考点；如果不存在，再去 `wiki/concepts/` 新建纯粹的理论概念卡片（需包含标准 Frontmatter 和必要的双向链接 `[[Wikilinks]]`）。
> 3. **挂链**：使用双向链接（例如 `👉 核心原理见：[[相关卡片名]]`）指向该知识卡片。
> 4. **更新索引**：若是新建了卡片，请务必前往 `wiki/Wiki Index_维基索引.md`，在相应的技术分类下增加新卡片的全局导航链接。
> 5. **知识点互联**：在相关卡片中，使用双向链接（例如 `👉 相关知识点：[[相关卡片名]]`）指向该卡片，形成知识点之间的互联。

这是一份增量维护的 Java 面试复习地图 (Map of Content)。具体题目清单已拆分到各专题子文件，此处仅作为进度罗盘。

## 进度总览

| 专题 | 子文件 | 进度 |
|---|---|---|
| 并发编程与多线程 | [[java-interview-concurrency]] | 3 / 6 |
| JVM 虚拟机体系 | [[java-interview-jvm]] | 2 / 5 |
| Java 集合框架 | [[java-interview-collections]] | ✅ 4 / 4 |
| 设计模式 | [[java-interview-design-patterns]] | 0 / 10 |
| 框架与中间件 | [[java-interview-frameworks]] | PostgreSQL 14 / 14，其他待录入 |

## 专题索引

- **[[java-interview-concurrency]]** — JMM、`volatile`/`synchronized`、AQS、`ThreadLocal`、线程池
- **[[java-interview-jvm]]** — 内存结构、GC 算法、CMS vs G1、调优排查
- **[[java-interview-collections]]** — ✅ 已完成：集合框架、`ArrayList`、`HashMap`、`ConcurrentHashMap`
- **[[java-interview-design-patterns]]** — GoF 23 种：创建型 / 结构型 / 行为型、OCP / LSP 原则
- **[[java-interview-frameworks]]** — Spring / Redis / MySQL（待录入）、PostgreSQL ✅ 已完成
