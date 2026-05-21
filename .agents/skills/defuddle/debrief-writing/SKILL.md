---
name: debrief-writing
description: Debrief / post-mortem pattern of this vault.
---

# Skill: Debrief / Post-mortem Writing

复盘记录事故或重要事件。目标是**学习**，而非归咎责任。每个复盘都是无责的。

## 触发条件

- 生产事故（任何严重程度）
- 耗时 2 小时以上才诊断完成的 Bug
- 被证明错误且需回滚的技术决策
- Sprint 或项目结束（回顾性复盘）

## 编号和命名

`dev/debriefs/YYYY-MM-DD-short-slug.md`

## 前置元数据

```yaml
---
title: <一句话描述发生了什么>
incident-date: 2026-04-28
severity: low | medium | high | critical
duration-minutes: 45
related-projects: ["[[Project-X]]"]
related-adrs: ["[[ADR-0007]]"]
---