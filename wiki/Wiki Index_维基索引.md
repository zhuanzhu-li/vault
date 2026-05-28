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

- [Java 编程语言](Java%20Programming%20Language_Java编程语言.md) — 跨平台面向对象语言，企业级开发首选
- [Java 访问修饰符](Java%20Access%20Modifiers_Java访问修饰符.md) — private/default/protected/public 四种访问级别
- [Java 集合框架](Java%20Collection%20Framework_Java集合框架.md) — Java 常用数据结构（List, Set, Queue, Map）的核心接口与体系
- [ArrayList](ArrayList_ArrayList.md) — 基于动态数组实现的 List，查询快增删慢
- [HashMap](HashMap_HashMap.md) — 基于哈希表实现的 Map，JDK 1.8 引入红黑树优化
- [ConcurrentHashMap](ConcurrentHashMap_ConcurrentHashMap.md) — 高并发安全的哈希表，JDK 1.8 采用 CAS + Synchronized
- [AQS (AbstractQueuedSynchronizer)](AQS_AbstractQueuedSynchronizer.md) — 各种并发锁和同步器（如 ReentrantLock）的核心基础框架
- [Java 内存模型 (JMM)](Java%20Memory%20Model_Java%20内存模型.md) — 解决多线程并发下的可见性、有序性与原子性
- [JVM 运行时数据区](JVM%20Run-Time%20Data%20Areas_JVM%20运行时数据区.md) — 虚拟机栈、堆、方法区等内存物理划分
- [Volatile 关键字](Volatile_Volatile关键字.md) — 解决可见性与有序性，不保证原子性的并发关键字
- [Synchronized 关键字](Synchronized_Synchronized关键字.md) — 保证原子性与可见性的悲观锁，具有锁升级机制
- [G1 收集器](G1%20Garbage%20Collector_G1%20收集器.md) — 面向大内存、可预测停顿时间、基于 Region 的垃圾收集器
- [JVM 调优](JVM%20Tuning_JVM调优.md) — 调整 GC 与参数以平衡吞吐量、停顿时间与内存占用

### Spring

- [Spring AI](Spring%20AI.md) — Spring 生态系统中的 AI 工程应用框架
- [Retrieval Augmented Generation](Retrieval%20Augmented%20Generation.md) — 检索增强生成，增强 LLM 回答领域特定问题的模式
- [Vector Database](Vector%20Database.md) — 向量数据库，用于高效存储和查询高维向量嵌入
- [Structured Outputs](Structured%20Outputs.md) — 结构化输出，将 AI 生成映射为结构化数据（如 POJO）
- [ChatClient API](ChatClient%20API.md) — 用于与 AI 聊天模型通信的流畅 API

### 设计模式

- [GoF 设计模式](GoF%20Design%20Patterns_GoF设计模式.md) — 四人帮 23 种经典面向对象设计模式总览
- [创建型模式](Creational%20Patterns_创建型模式.md) — 工厂方法、抽象工厂、建造者、原型、单例
- [结构型模式](Structural%20Patterns_结构型模式.md) — 适配器、桥接、组合、装饰器、外观、享元、代理
- [行为型模式](Behavioral%20Patterns_行为型模式.md) — 责任链、命令、解释器、迭代器、中介、备忘录、观察者、状态、策略、模板方法、访问者

### 数据库

- [PostgreSQL](PostgreSQL_PostgreSQL.md) — 开源对象关系型数据库，MVCC、丰富索引类型与扩展生态
- [MVCC](MVCC_MVCC.md) — 多版本并发控制，数据库 ACID 的隔离性与一致性基石
- [WAL 预写日志](WAL_WAL.md) — 数据库事务持久性与崩溃恢复的核心机制
- [MySQL 设计规约](MySQL%20Design%20Guidelines_MySQL设计规约.md) — 阿里巴巴MySQL建表、索引、SQL、ORM规范

### 分布式系统与架构

- [CAP 与 BASE 理论](CAP%20and%20BASE%20Theory_CAP与BASE理论.md) — CAP 三选二、BASE 柔性事务、分布式一致性
- [分布式 ID 生成](Distributed%20ID%20Generation_分布式ID生成.md) — Snowflake、Leaf 号段、ULID 算法
- [分布式锁](Distributed%20Lock_分布式锁.md) — Redis 分布式锁、RedLock、ZooKeeper 锁
- [分布式事务](Distributed%20Transaction_分布式事务.md) — 2PC/3PC、TCC、Saga、Seata
- [一致性哈希](Consistent%20Hashing_一致性哈希.md) — Hash 环、虚拟节点、数据迁移最小化
- [接口幂等设计](Interface%20Idempotency%20Design_接口幂等设计.md) — Token机制、唯一索引、乐观锁
- [延迟任务队列](Delayed%20Task%20Queue_延迟任务队列.md) — 时间轮算法、Redis ZSet、RabbitMQ 延迟消息
- [缓存架构设计](Cache%20Architecture%20Design_缓存架构设计.md) — 穿透/击穿/雪崩、淘汰策略、容量规划

### 系统设计与架构原则

- [SOLID 原则](SOLID%20Principles_SOLID原则.md) — 单一职责、开闭、里氏替换、接口隔离、依赖倒置
- [架构设计中的设计模式](Design%20Patterns%20in%20Architecture_架构设计中的设计模式.md) — 创建型/结构型/行为型模式分层应用
- [领域驱动设计](Domain-Driven%20Design_领域驱动设计.md) — 聚合根、限界上下文、事件溯源

### 微服务与云原生

- [服务治理](Service%20Governance_服务治理.md) — 熔断降级、限流、负载均衡、服务发现
- [配置中心](Configuration%20Center_配置中心.md) — Apollo、Nacos、热更新、多环境隔离
- [API 网关](API%20Gateway_API网关.md) — Spring Cloud Gateway、路由、鉴权、限流
- [Docker 容器化](Docker%20Containerization_Docker容器化.md) — 镜像构建、Compose 编排、网络存储
- [nerdctl](nerdctl_nerdctl.md) — containerd 官方 CLI，Docker 兼容的高级容器运行时
- [Kubernetes](Kubernetes_Kubernetes.md) — Pod/Service/Deployment、调度、网络、存储
- [CI/CD 实践](CI%20CD%20Practices_CI-CD实践.md) — GitLab CI、Jenkins Pipeline、蓝绿部署、金丝雀发布

### 高并发与性能

- [秒杀系统架构](Seckill%20System%20Architecture_秒杀系统架构.md) — 流量削峰、库存扣减、MQ 异步下单、防刷

### 代码质量与工程规范

- [代码质量规范](Code%20Quality%20Standards_代码质量规范.md) — 强制/推荐/参考三级约束力的编程规范体系
- [Java 命名规范](Java%20Naming%20Conventions_Java命名规范.md) — 驼峰命名、常量命名、包名规范、领域模型命名
- [异常处理规范](Exception%20Handling%20Standards_异常处理规范.md) — NPE防护、异常匹配、资源关闭规范
- [日志规约](Logging%20Standards_日志规约.md) — 日志级别选择、输出规范、内容规范
- [安全规约](Security%20Guidelines_安全规约.md) — 权限控制、数据脱敏、SQL注入防护、CSRF防护
- [工程结构规约](Project%20Structure%20Standards_工程结构规约.md) — 应用分层、二方库依赖、服务器配置

### 大数据与分布式系统

- [Apache HBase](Apache%20HBase_Apache%20HBase.md) — 运行于 HDFS 之上的分布式 NoSQL 大数据存储系统
- [分布式计算](Distributed%20Computing_分布式计算.md) — 分布式系统的挑战、故障来源与八大谬误
- [支持与测试预期](Support%20&%20Testing%20Expectations_支持与测试预期.md) — 开源项目中 supported/unsupported/tested/not tested 的术语体系
- [负责任披露](Responsible%20Disclosure_负责任披露.md) — 安全漏洞的私有报告与公开披露实践

### 容器与编排

- [Kubernetes 网络子网](Kubernetes%20网络子网_Kubernetes网络子网.md) — 三类子网（节点/Pod/Service CIDR）的划分与约束
- [kube-proxy iptables 模式](kube-proxy%20iptables%20模式_kube-proxy%20iptables模式.md) — KUBE-SERVICES 链的 DNAT 负载均衡机制
- [kube-proxy IPVS 模式](kube-proxy%20IPVS%20模式_kube-proxy%20IPVS模式.md) — 基于哈希表 O(1) 查找的替代模式，适合大规模集群
- [内核网络参数](内核网络参数_内核网络参数.md) — bridge-nf-call-iptables、ip_forward 等关键 sysctl 配置
- [ip-masq-agent](ip-masq-agent_ip-masq-agent.md) — 管理 Pod 访问外网的 SNAT 行为

### 知识管理

- [文件优于应用](File%20Over%20App_文件优于应用.md) — 哲学：控制文件，而非应用
- [数字耐久性](Digital%20Durability_数字耐久性.md) — 产物可跨越数十年阅读
- [软件易逝性](Software%20Ephemerality_软件易逝性.md) — 一切应用都是暂时的
- [用户数据所有权](User%20Data%20Ownership_用户数据所有权.md) — 你控制自己的数据
- [纯文本知识库](Plain%20Text%20Knowledge%20Base_纯文本知识库.md) — 以 Markdown 为先的个人知识管理
- [开放文件格式](Open%20File%20Formats_开放文件格式.md) — 可互操作、长寿命的格式

### AI 编码与 Harness

- [开发者学习策略](Developer%20Learning%20Strategies_开发者学习策略.md) — 开发者技能金字塔、刻意练习、知识管理体系
- [Harness 工程](Harness%20Engineering_Harness%20工程.md) — Claude Code 宿主框架；hooks 确定性执行
- [AI Harness Engineering](AI%20Harness%20Engineering.md) — 规范 AI 编程助手的代码规范与流程引擎
- [Coding Harness](Coding%20Harness.md) — 代码层面的强制规则与架构约束
- [Process Harness](Process%20Harness.md) — 利用思维链规范 AI 的研发与分析流程
- [基础提示技巧](Basic%20Prompting%20Techniques_基础提示技巧.md) — 零样本、少样本、角色提示、系统提示
- [Chain of Thought](Chain%20of%20Thought.md) — 引导语言模型逐步推理的提示词策略
- [输出控制](Output%20Control_输出控制.md) — 结构化输出、约束解码、温度与采样参数
- [提示词陷阱](Prompt%20Pitfalls_提示词陷阱.md) — 指令泄露、约束脆弱性、过度拟合、越狱攻击
- [上下文压缩](Context%20Compact_上下文压缩.md) — 95% 触发；口头约束易丢失
- [编码 Hooks](AI%20Coding%20Hooks_编码%20Hooks.md) — PostToolUse / PreToolUse；exit 2 阻断
- [Subagent 上下文隔离](Subagent%20Context%20Isolation_Subagent%20上下文隔离.md) — 高 token 操作隔离
- [项目上下文持久化](Project%20Context%20Persistence_项目上下文持久化.md) — CLAUDE.md + Memory
- [语义数据准确率](Semantic%20Data%20Accuracy_语义数据准确率.md) — 语义 × 规范覆盖度
- [数仓 AI 工作流](DW%20AI%20Workflow_数仓%20AI%20工作流.md) — 8 步 SKILL 与 Harness 分工

### GIS 基础

- [GIS 坐标系与地图投影](GIS%20Coordinate%20Systems_GIS坐标系.md) — WGS84、GCJ-02及Web Mercator等
- [空间数据模型](Spatial%20Data%20Models_空间数据模型.md) — 矢量数据与栅格数据的结构差异
- [空间分析](Spatial%20Analysis_空间分析.md) — 缓冲区、叠加及网络分析等拓扑计算
- [WebGIS](WebGIS.md) — OGC标准及主流GIS技术栈
- [GeoTools 架构](GeoTools%20Architecture_GeoTools架构.md) — Java GIS 开源库的核心架构
- [JTS](JTS_Java%20Topology%20Suite.md) — Java 空间几何与拓扑计算库
- [CQL](CQL_Common%20Query%20Language.md) — 地理空间通用查询语言
- [GeoMesa](GeoMesa_GeoMesa.md) — 开源时空大数据引擎，为 HBase/Cassandra 提供时空索引

## 实体

- [阿里巴巴](Alibaba_阿里巴巴.md) — 中国最大电商公司，Java开发手册发布方，p3c/Arthas/Druid/Nacos等开源工具贡献者
- [Cursor](Cursor.md) — 深度集成 AI 功能的现代代码编辑器
- [斯蒂芬·安戈](Steph%20Ango_斯蒂芬·安戈.md) — Obsidian CEO；*File over app* 作者
- [黑曜石](Obsidian_黑曜石.md) — 本地优先笔记应用
- [得物](Dewu_得物.md) — 数仓 Harness 实践来源
- [Claude Code](Claude%20Code_Claude%20Code.md) — Harness 宿主工具
- [丹克](Danke_丹克.md) — 得物 Harness 文章作者
- [OGC](OGC_开放地理空间信息联盟.md) — 开放地理空间信息联盟，制定 WMS/WFS 等标准
- [GeoTools](GeoTools.md) — Java WebGIS 主流开源工具库
- [OSGeo](OSGeo_开源地理空间基金会.md) — 开源地理空间基金会
- [Spring Framework](Spring%20Framework.md) — Java 平台的广泛应用框架和控制反转容器
- [Broadcom](Broadcom.md) — 跨国科技公司，Spring 生态系统所有者
- [OpenAI](OpenAI.md) — 人工智能研究实验室和公司，AI 模型主要提供商
- [Prompt Engineering](Prompt%20Engineering.md) — 提示词工程，设计和优化 LLM 输入提示的方法论
- [GoF 设计模式](GoF%20Design%20Patterns_GoF设计模式.md) — 四人帮 23 种经典设计模式，含创建型/结构型/行为型三大类
- [Apache 软件基金会](Apache%20Software%20Foundation_Apache软件基金会.md) — 开源软件非营利组织，托管 HBase、Hadoop 等 350+ 项目
- [Fumadocs](Fumadocs_Fumadocs.md) — 基于 MDX 的现代文档框架，驱动 HBase 官方文档站点
- [Flannel](Flannel_Flannel.md) — 轻量级 CNI 网络插件，使用 VXLAN overlay 实现 Pod 跨节点通信
- [Calico](Calico_Calico.md) — 支持 BGP 路由和网络策略的 CNI 网络插件

## 已导入来源

- [2026-05-21-ai-harness-engineering-conversation](raw/clippings/2026-05-21-ai-harness-engineering-conversation.md) — Agent & User, 2026-05-21
- [2026-05-21-gis-fundamentals](raw/clippings/2026-05-21-gis-fundamentals.md) — Agent & User, 2026-05-21
- [2026-05-20-file-over-app](raw/clippings/2026-05-20-file-over-app.md) — 斯蒂芬·安戈，2023-07-01
- [2026-05-20-claude-code-harness-dw-dewu](raw/clippings/2026-05-20-claude-code-harness-dw-dewu.md) — 丹克 / 得物技术，2026
- [2026-05-21-geotools-user-guide](raw/clippings/2026-05-21-geotools-user-guide.md) — OSGeo, 2026-05-21
- [2026-05-21-spring-ai](raw/clippings/2026-05-21-spring-ai.md) — Spring, 2026-05-21
- [2026-05-22-postgresql-interview-questions](raw/clippings/2026-05-22-postgresql-interview-questions.md) — AI Agent, 2026-05-22
- [2026-05-22-postgresql-deep-dive](raw/clippings/2026-05-22-postgresql-deep-dive.md) — AI Agent, 2026-05-22
- [2026-05-22-java-design-patterns-liaoxuefeng](raw/clippings/2026-05-22-java-design-patterns-liaoxuefeng.md) — 廖雪峰，2026-05-22
- [2026-05-25-hbase-reference-guide-preface](raw/clippings/2026-05-25-hbase-reference-guide-preface.md) — Apache HBase Community，2026-05-25
- [2026-05-25-k8s-networking-subnet-to-iptables](raw/clippings/2026-05-25-k8s-networking-subnet-to-iptables.md) — User，2026-05-25
- [2026-05-26-alibaba-java-development-manual](raw/clippings/2026-05-26-alibaba-java-development-manual.md) — 阿里巴巴集团技术团队，2026-05-26
- [2026-05-26-geomesa-quickstart-hbase](raw/clippings/2026-05-26-geomesa-quickstart-hbase.md) — GeoMesa Community，2026-05-26
- [2026-05-26-nerdctl](raw/clippings/2026-05-26-nerdctl.md) — AI Agent & User，2026-05-26
- [2026-05-28-java-developer-roadmap](raw/clippings/2026-05-28-java-developer-roadmap.md) — roadmap.sh，2026-05-28
- [2026-05-28-java-access-modifiers](raw/clippings/2026-05-28-java-access-modifiers.md) — Jenkov，2026-05-28
- [2026-05-28-dev-essentials](raw/clippings/2026-05-28-dev-essentials.md) — Jakob Jenkov，2026-05-28
