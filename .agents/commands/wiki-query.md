---
description: Query the vault knowledge base
argument-hint: <question in natural language>
allowed-tools: Bash(grep:*), Bash(find:*), Bash(cat:*)
---

我将通过查询知识库来回答以下问题：**$ARGUMENTS**

1. **搜索：** 使用 `grep -r -l --include="*.md"` 在整个知识库中查找，优先从 wiki/ 区域开始
2. **聚焦阅读：** 读取最多 10 个候选文件
3. **综合回答：** 用直接散文形式回答，始终通过 [[wikilinks]] 引用咨询过的文件
4. **如果知识库信息不足：** 直接说明，不要编造
5. **建议后续步骤**（如适用）