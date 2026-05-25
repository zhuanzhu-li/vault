---
title: 负责任披露
type: concept
tags: [security, open-source, vulnerability-management]
aliases:
  - Responsible Disclosure
  - 负责任披露
  - 安全漏洞披露
sources:
  - "[2026-05-25-hbase-reference-guide-preface](raw/clippings/2026-05-25-hbase-reference-guide-preface.md)"
---

# 负责任披露

安全领域的一种漏洞处理实践：在向公众公开漏洞细节之前，先向项目维护者报告，使其有时间开发并发布修复版本。

## 在 Apache HBase 中的实践

[Apache HBase](Apache%20HBase_Apache%20HBase.md) 项目对漏洞报告采取双重渠道策略：

- **非安全 Bug**：通过公开的 [JIRA](https://issues.apache.org/jira/browse/hbase) 报告
- **安全漏洞**：通过私有邮件列表 [private@hbase.apache.org](mailto:private@hbase.apache.org) 报告——任何人可以向该地址发送邮件，但只有授权成员可以阅读

这种设计确保：
1. 漏洞细节在修复完成前不会公开暴露
2. 任何人都可以方便地提交报告（发送权限不受限制）
3. 保护现有 HBase 安装不受新漏洞的利用威胁

## 相关

- [Apache HBase](Apache%20HBase_Apache%20HBase.md)
- [Apache 软件基金会](Apache%20Software%20Foundation_Apache软件基金会.md)
- [支持与测试预期](Support%20&%20Testing%20Expectations_支持与测试预期.md)
