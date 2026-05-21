
---
name: adr-writing
description: Architecture Decision Records (ADRs) pattern of this vault.
---

# Skill: ADR Writing

ADR 格式基于 Michael Nygard 的模式，经过适应调整。每个 ADR 是 `dev/adr/` 下的一个 `.md` 文件。

## 编号规则

文件名：`dev/adr/ADR-NNNN-short-slug.md`。NNNN 是下一个可用整数，零填充到 4 位。
例如：`ADR-0007-use-pgvector-for-rag.md`

在创建新 ADR 之前，读取 `dev/adr/` 中的所有文件以查找下一个编号，并检测是否已存在相同主题的 ADR（如果存在，更新已有 ADR 而非创建新）。

## 必需的前置元数据

```yaml
---
title: Use pgvector for RAG storage
decision-date: 2026-05-01
deciders: [me, Roan]
status: proposed | accepted | deprecated | superseded
tags: [rag, postgres, vector-db]
supersedes: []  # ADR-XXXX if applicable
---
