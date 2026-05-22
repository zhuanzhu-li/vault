---
title: 编码 Hooks
type: concept
tags: [ai-coding, claude-code, automation, validation]
aliases:
  - AI Coding Hooks
  - 编码 Hooks
  - Claude Code Hooks
sources:
  - "[2026-05-20-claude-code-harness-dw-dewu](raw/clippings/2026-05-20-claude-code-harness-dw-dewu.md)"
---

# 编码 Hooks

[Harness 工程](Harness%20Engineering_Harness%20工程.md) 在 LLM 推理循环**之外**、由 `settings.json` 配置的确定性回调。数仓场景中用于把规范从「LLM 记忆」变为「每次写文件后强制检查」。

## 常见 Hook 类型

| Hook | 触发时机 | 数仓示例 |
|------|----------|----------|
| PostToolUse | Write/Edit 后 | `validate_sql.sh` 检查 SELECT *、PARTITION |
| PreToolUse | Bash 前 | 拦截生产表 DROP/TRUNCATE |
| SessionStart (compact) | compact 后 | 重注入 `dw_conventions.md` |
| Stop | 会话结束前 | 检查任务是否完成 |

## 关键协议

- **阻断必须用 `exit 2`** — `exit 1` 不会阻止 Claude 继续执行
- Hook 在 Harness 层运行，不占用 Claude 推理 token

## 效果（得物实践数据）

规范遵守率从依赖 prompt 的 **70%~80%** 提升到 hook 强制的 **95%+**。

## 相关

- [Harness 工程](Harness%20Engineering_Harness%20工程.md)
- [数仓 AI 工作流](DW%20AI%20Workflow_数仓%20AI%20工作流.md)
- [项目上下文持久化](Project%20Context%20Persistence_项目上下文持久化.md)
