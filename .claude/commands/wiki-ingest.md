---
description: Ingest a URL or file into the vault, distilling to the wiki
argument-hint: <URL | file path>
allowed-tools: Bash(curl:*), Bash(cat:*), Bash(ls:*), WebFetch
---

请遵循 CLAUDE.md 中定义的导入流程。

1. **确定来源类型：**
   - URL → 使用 `defuddle` 技能提取纯净内容
   - 本地文件 → 用 `cat` 直接读取

2. **保存原始内容：**
   - 路径：`raw/clippings/YYYY-MM-DD-slug.md`
   - Slug 从标题派生（kebab-case，最长 60 字符）
   - 前置元数据包含：source-url、captured-date、title、author

3. **分析：**
   - 识别 3-7 个关键概念
   - 识别 1-3 个实体（人/公司）
   - 检查 `wiki/concepts/` 和 `wiki/entities/` 中是否已存在

4. **在执行前展示计划**，等待审批
5. **执行计划**，最后以列表形式报告每个受影响的文件