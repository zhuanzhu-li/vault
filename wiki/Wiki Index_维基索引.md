---
title: 维基索引
type: synthesis
tags: [index, knowledge-management]
aliases:
  - Wiki Index
  - 维基索引
sources: []
---

# 维基索引

由 agent 维护的全局索引。

## 概念

### Java 体系与 JVM

- [[Java Collection Framework_Java集合框架|Java 集合框架]] — Java 常用数据结构（List, Set, Queue, Map）的核心接口与体系
- [[ArrayList_ArrayList|ArrayList]] — 基于动态数组实现的 List，查询快增删慢
- [[HashMap_HashMap|HashMap]] — 基于哈希表实现的 Map，JDK 1.8 引入红黑树优化
- [[ConcurrentHashMap_ConcurrentHashMap|ConcurrentHashMap]] — 高并发安全的哈希表，JDK 1.8 采用 CAS + Synchronized
- [[AQS_AbstractQueuedSynchronizer|AQS (AbstractQueuedSynchronizer)]] — 各种并发锁和同步器（如 ReentrantLock）的核心基础框架
- [[Java Memory Model_Java 内存模型|Java 内存模型 (JMM)]] — 解决多线程并发下的可见性、有序性与原子性
- [[JVM Run-Time Data Areas_JVM 运行时数据区|JVM 运行时数据区]] — 虚拟机栈、堆、方法区等内存物理划分
- [[Volatile_Volatile关键字|Volatile 关键字]] — 解决可见性与有序性，不保证原子性的并发关键字
- [[Synchronized_Synchronized关键字|Synchronized 关键字]] — 保证原子性与可见性的悲观锁，具有锁升级机制
- [[G1 Garbage Collector_G1 收集器|G1 收集器]] — 面向大内存、可预测停顿时间、基于 Region 的垃圾收集器
- [[JVM Tuning_JVM调优|JVM 调优]] — 调整 GC 与参数以平衡吞吐量、停顿时间与内存占用

### Spring

- [[Spring AI]] — Spring 生态系统中的 AI 工程应用框架
- [[Retrieval Augmented Generation]] — 检索增强生成，增强 LLM 回答领域特定问题的模式
- [[Vector Database]] — 向量数据库，用于高效存储和查询高维向量嵌入
- [[Structured Outputs]] — 结构化输出，将 AI 生成映射为结构化数据（如 POJO）
- [[ChatClient API]] — 用于与 AI 聊天模型通信的流畅 API

### 知识管理

- [[File Over App_文件优于应用|文件优于应用]] — 哲学：控制文件，而非应用
- [[Digital Durability_数字耐久性|数字耐久性]] — 产物可跨越数十年阅读
- [[Software Ephemerality_软件易逝性|软件易逝性]] — 一切应用都是暂时的
- [[User Data Ownership_用户数据所有权|用户数据所有权]] — 你控制自己的数据
- [[Plain Text Knowledge Base_纯文本知识库|纯文本知识库]] — 以 Markdown 为先的个人知识管理
- [[Open File Formats_开放文件格式|开放文件格式]] — 可互操作、长寿命的格式

### AI 编码与 Harness

- [[Harness Engineering_Harness 工程|Harness 工程]] — Claude Code 宿主框架；hooks 确定性执行
- [[AI Harness Engineering]] — 规范 AI 编程助手的代码规范与流程引擎
- [[Coding Harness]] — 代码层面的强制规则与架构约束
- [[Process Harness]] — 利用思维链规范 AI 的研发与分析流程
- [[Chain of Thought]] — 引导语言模型逐步推理的提示词策略
- [[Context Compact_上下文压缩|上下文压缩]] — 95% 触发；口头约束易丢失
- [[AI Coding Hooks_编码 Hooks|编码 Hooks]] — PostToolUse / PreToolUse；exit 2 阻断
- [[Subagent Context Isolation_Subagent 上下文隔离|Subagent 上下文隔离]] — 高 token 操作隔离
- [[Project Context Persistence_项目上下文持久化|项目上下文持久化]] — CLAUDE.md + Memory
- [[Semantic Data Accuracy_语义数据准确率|语义数据准确率]] — 语义 × 规范覆盖度
- [[DW AI Workflow_数仓 AI 工作流|数仓 AI 工作流]] — 8 步 SKILL 与 Harness 分工

### GIS 基础

- [[GIS Coordinate Systems_GIS坐标系|GIS 坐标系与地图投影]] — WGS84、GCJ-02及Web Mercator等
- [[Spatial Data Models_空间数据模型|空间数据模型]] — 矢量数据与栅格数据的结构差异
- [[Spatial Analysis_空间分析|空间分析]] — 缓冲区、叠加及网络分析等拓扑计算
- [[WebGIS]] — OGC标准及主流GIS技术栈
- [[GeoTools Architecture_GeoTools架构|GeoTools 架构]] — Java GIS 开源库的核心架构
- [[JTS_Java Topology Suite|JTS]] — Java 空间几何与拓扑计算库
- [[CQL_Common Query Language|CQL]] — 地理空间通用查询语言

## 实体

- [[Cursor]] — 深度集成 AI 功能的现代代码编辑器
- [[Steph Ango_斯蒂芬·安戈|斯蒂芬·安戈]] — Obsidian CEO；*File over app* 作者
- [[Obsidian_黑曜石|黑曜石]] — 本地优先笔记应用
- [[Dewu_得物|得物]] — 数仓 Harness 实践来源
- [[Claude Code_Claude Code|Claude Code]] — Harness 宿主工具
- [[Danke_丹克|丹克]] — 得物 Harness 文章作者
- [[OGC_开放地理空间信息联盟|OGC]] — 开放地理空间信息联盟，制定 WMS/WFS 等标准
- [[GeoTools]] — Java WebGIS 主流开源工具库
- [[OSGeo_开源地理空间基金会|OSGeo]] — 开源地理空间基金会
- [[Spring Framework]] — Java 平台的广泛应用框架和控制反转容器
- [[Broadcom]] — 跨国科技公司，Spring 生态系统所有者
- [[OpenAI]] — 人工智能研究实验室和公司，AI 模型主要提供商

## 已导入来源

- [[raw/clippings/2026-05-21-ai-harness-engineering-conversation]] — Agent & User, 2026-05-21
- [[raw/clippings/2026-05-21-gis-fundamentals]] — Agent & User, 2026-05-21
- [[raw/clippings/2026-05-20-file-over-app]] — 斯蒂芬·安戈，2023-07-01
- [[raw/clippings/2026-05-20-claude-code-harness-dw-dewu]] — 丹克 / 得物技术，2026
- [[raw/clippings/2026-05-21-geotools-user-guide]] — OSGeo, 2026-05-21
- [[raw/clippings/2026-05-21-spring-ai]] — Spring, 2026-05-21
