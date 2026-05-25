---
title: 支持与测试预期
type: concept
tags: [open-source, software-quality, testing, documentation]
aliases:
  - Support and Testing Expectations
  - 支持与测试预期
sources:
  - "[2026-05-25-hbase-reference-guide-preface](raw/clippings/2026-05-25-hbase-reference-guide-preface.md)"
---

# 支持与测试预期

开源项目中定义功能可靠程度的术语体系。[Apache HBase](Apache%20HBase_Apache%20HBase.md) 文档中明确区分了四个层级，以帮助用户理解各项功能的使用风险。

## 术语定义

### Supported（支持）

功能按文档描述的方式正常工作，如有偏差应视为 Bug 并报告。

### Not Supported（不支持）

特定的使用方式或模式预期无法正常工作，应视为反模式（antipattern）。如需重新评估某个功能的支持状态，可通过 JIRA 或邮件列表发起讨论。

### Tested（已测试）

功能有单元测试或集成测试覆盖，已证明能按预期工作。

### Not Tested（未测试）

功能在某种使用方式下可能工作也可能不工作，可能损坏数据或引发运维问题——属于未知领域，无任何保证。如果用户能提供证据证明某项标记为"未测试"的功能确实能在特定方式下工作，欢迎提交测试用例和指标。

## 意义

这套术语为开源项目的用户提供了清晰的预期管理：
- 帮助用户判断生产环境中可以使用哪些功能
- 鼓励社区通过测试贡献将"未测试"的功能提升为"已测试"
- 为文档编写提供了标准化的表达框架

## 相关

- [Apache HBase](Apache%20HBase_Apache%20HBase.md)
- [分布式计算](Distributed%20Computing_分布式计算.md)
- [负责任披露](Responsible%20Disclosure_负责任披露.md)
