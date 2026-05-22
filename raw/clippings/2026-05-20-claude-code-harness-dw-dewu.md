---
source-url: https://mp.weixin.qq.com/s/KmQJU7nXmYh5qgWPj4ajlw
captured-date: 2026-05-20
title: Claude Code Harness 工程：数仓侧落地方案
original-title: Claude Code Harness 工程：数仓侧落地方案｜得物技术
author: 丹克
published: 2026-03-01
publisher: 得物技术
tags:
  - ai-coding
---

# Claude Code Harness 工程：数仓侧落地方案

> 来源：得物技术 · 作者丹克

## 一、AI Coding 现状与痛点

得物离线数仓各小组已基本覆盖 Claude Code 及数据平台 IDE 插件。重复性工作提效明显，但仍有三类结构性痛点：

**痛点一：上下文失忆** — 口头约束（如 amount 单位为千元）在 [上下文压缩](Context%20Compact_上下文压缩.md) 后丢失，SQL 可能差 1000 倍。

**痛点二：规范执行不稳定** — OneData 命名、INSERT 必须 PARTITION 等规范，人工遵守率 60%~70%，纯 prompt 约 70%~80%。需把规范从「LLM 记忆」变为「强制护栏」。

**痛点三：context 迅速膨胀** — 自测 SQL 结果、SKILL 全文、数据比对样本等撑满 context → compact → 关键约束遗忘。

核心结论：规范执行是人的短板、AI 的长板；业务判断是 AI 的短板、人的长板。[Harness 工程](Harness%20Engineering_Harness%20工程.md) 目标是把执行层不稳定因素系统性消掉。

## 二、Harness 是什么

Claude Code update-config skill 描述：「Automated behaviors require hooks configured in settings.json — the harness executes these, not Claude」。

**Harness** = [Claude Code](Claude%20Code_Claude%20Code.md) 宿主运行框架（工具链容器），负责：

- 管理 context window 生命周期
- 在 LLM 推理循环之外**确定性**执行 hooks
- 协调 subagents 生命周期
- 不依赖模型判断，直接执行配置的自动化行为

## 三、compact 丢掉什么

auto-compact 在 context 约 **95%** 时触发，对话历史替换为摘要，token 约降至原来的 **12%**。

丢失的典型内容：口头迭代约束、SKILL 读了一半的前几步、大量自测/血缘原始输出。

## 四、五层防御体系

1. **CLAUDE.md** — 每次 compact 后从磁盘重注入；写入迭代状态与全局规范（≤100 行）
2. **Auto Memory** — `~/.claude/projects/<project>/memory/MEMORY.md` 跨会话积累踩坑
3. **Hooks** — PostToolUse 写 SQL 后自动 `validate_sql.sh`；PreToolUse 拦截危险 DDL；`exit 2` 才阻断
4. **Subagents** — sql-validator、dw-explorer、data-quality-checker 等隔离高 token 操作
5. **SKILL 改造** — 主会话不加载全文 SKILL；subagent 内读 SKILL，主 context 只收摘要；path-scoped rules 按需加载

## 五、数仓 Harness 架构

三层职责：

| 层 | 机制 | 解决问题 |
|----|------|----------|
| 持久化层 | CLAUDE.md + Memory | 失忆 |
| Harness 层 | Hooks | 规范靠记忆 |
| Subagent 层 | 独立 context | context 撑满 |

8 步研发流程：需求分析 → 技术设计 → ETL → 自测 → 数据比对 → SR 导入 → 性能优化 → SLA/DQC。高 token 步骤（ETL 验证、自测、比对、血缘）走 Harness / subagent。

## 六、SKILL 与工作流

- 主对话只读决策级信息（PASS/FAIL、差异字段、优化方案）
- `/dw-etl` 等命令封装规范 + 产出格式 + hook 护栏 + subagent 验证
- 规范遵守率：hook 强制后预期 **95%+**；compact 频率预期降 **50%~70%**

## 七、落地三步

1. 创建 `.claude/CLAUDE.md` 写入迭代状态
2. 配置 `.claude/settings.json` + `hooks/`
3. 创建 subagents：`sql-validator.md`、`dw-explorer.md`、`data-quality-checker.md`

## 八、核心问题与公式

数仓 AI 开发瓶颈是**语义理解**（非 SQL 能力）：需求理解偏差占返工 40%~60%。

```
准确率 = 语义理解深度 × 数据规范覆盖度
```

Harness 把语义与规范从 LLM 记忆迁移到 hooks + 持久化文件，使等式两边变量稳定。

四类可解问题：字段口径遗忘、需求理解偏差、SQL 规范不一致、大型需求 context 耗尽。

终极目标：从「AI 帮我写代码」到「AI 嵌入研发流水线」—— LLM 负责语义与设计，Harness 负责确定性检查，Subagent 负责重读取，CLAUDE.md + Memory 负责跨会话状态。

## 百科链接

- [Harness 工程](Harness%20Engineering_Harness%20工程.md)
- [上下文压缩](Context%20Compact_上下文压缩.md)
- [编码 Hooks](AI%20Coding%20Hooks_编码%20Hooks.md)
- [Subagent 上下文隔离](Subagent%20Context%20Isolation_Subagent%20上下文隔离.md)
- [项目上下文持久化](Project%20Context%20Persistence_项目上下文持久化.md)
- [语义数据准确率](Semantic%20Data%20Accuracy_语义数据准确率.md)
- [数仓 AI 工作流](DW%20AI%20Workflow_数仓%20AI%20工作流.md)
- [得物](Dewu_得物.md)
- [Claude Code](Claude%20Code_Claude%20Code.md)
- [丹克](Danke_丹克.md)
