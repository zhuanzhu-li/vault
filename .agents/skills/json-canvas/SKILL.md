---
name: json-canvas
description: 创建和编辑 JSON Canvas 文件（.canvas），包含节点、边、组和连接。在处理 .canvas 文件、创建可视化画布、思维导图、流程图或用户在 Obsidian 中提到 Canvas 文件时使用。
---

# JSON Canvas 技能

## 文件结构

一个 canvas 文件（`.canvas`）包含两个顶级数组，遵循 [JSON Canvas 规范 1.0](https://jsoncanvas.org/spec/1.0/)：

```json
{
  "nodes": [],
  "edges": []
}
```

- `nodes`（可选）：节点对象数组
- `edges`（可选）：连接节点的边对象数组

## 常见工作流

### 1. 创建新画布

1. 创建一个具有基本结构 `{"nodes": [], "edges": []}` 的 `.canvas` 文件
2. 为每个节点生成唯一的 16 字符十六进制 ID（例如 `"6f0ad84f44ce9c17"`）
3. 添加包含必填字段的节点：`id`、`type`、`x`、`y`、`width`、`height`
4. 添加通过 `fromNode` 和 `toNode` 引用有效节点 ID 的边
5. **验证**：解析 JSON 以确认其有效。验证所有 `fromNode`/`toNode` 值是否存在于 nodes 数组中

### 2. 向现有画布添加节点

1. 读取并解析现有的 `.canvas` 文件
2. 生成一个与现有节点或边 ID 不冲突的唯一 ID
3. 选择避免与现有节点重叠的位置（`x`，`y`）（保留 50-100px 间距）
4. 将新节点对象追加到 `nodes` 数组
5. 可选地添加将新节点连接到现有节点的边
6. **验证**：确认所有 ID 都是唯一的，并且所有边引用都解析为现有节点

### 3. 连接两个节点

1. 确定源节点和目标节点的 ID
2. 生成唯一的边 ID
3. 将 `fromNode` 和 `toNode` 设置为源和目标 ID
4. 可选地设置 `fromSide`/`toSide`（top、right、bottom、left）作为锚点
5. 可选地设置 `label` 以在边上显示描述性文本
6. 将边追加到 `edges` 数组
7. **验证**：确认 `fromNode` 和 `toNode` 都引用现有的节点 ID

### 4. 编辑现有画布

1. 将 `.canvas` 文件作为 JSON 读取并解析
2. 通过 `id` 找到目标节点或边
3. 修改所需的属性（文本、位置、颜色等）
4. 将更新后的 JSON 写回文件
5. **验证**：编辑后重新检查所有 ID 的唯一性和边引用的完整性

## 节点

节点是放置在画布上的对象。数组顺序决定了 z-index：第一个节点 = 底层，最后一个节点 = 顶层。

### 通用节点属性

| 属性 | 必填 | 类型 | 描述 |
|-----------|----------|------|-------------|
| `id` | 是 | string | 唯一的 16 字符十六进制标识符 |
| `type` | 是 | string | `text`、`file`、`link` 或 `group` |
| `x` | 是 | integer | X 坐标（像素） |
| `y` | 是 | integer | Y 坐标（像素） |
| `width` | 是 | integer | 宽度（像素） |
| `height` | 是 | integer | 高度（像素） |
| `color` | 否 | canvasColor | 预设 `"1"`-`"6"` 或十六进制（例如 `"#FF0000"`） |

### 文本节点

| 属性 | 必填 | 类型 | 描述 |
|-----------|----------|------|-------------|
| `text` | 是 | string | 包含 Markdown 语法的纯文本 |

```json
{
  "id": "6f0ad84f44ce9c17",
  "type": "text",
  "x": 0,
  "y": 0,
  "width": 400,
  "height": 200,
  "text": "# Hello World\n\nThis is **Markdown** content."
}
```

**换行陷阱**：在 JSON 字符串中使用 `\n` 进行换行。**不要**使用字面量 `\\n` —— Obsidian 会将其渲染为字符 `\` 和 `n`。

### 文件节点

| 属性 | 必填 | 类型 | 描述 |
|-----------|----------|------|-------------|
| `file` | 是 | string | 系统内文件的路径 |
| `subpath` | 否 | string | 链接到标题或块（以 `#` 开头） |

```json
{
  "id": "a1b2c3d4e5f67890",
  "type": "file",
  "x": 500,
  "y": 0,
  "width": 400,
  "height": 300,
  "file": "Attachments/diagram.png"
}
```

### 链接节点

| 属性 | 必填 | 类型 | 描述 |
|-----------|----------|------|-------------|
| `url` | 是 | string | 外部 URL |

```json
{
  "id": "c3d4e5f678901234",
  "type": "link",
  "x": 1000,
  "y": 0,
  "width": 400,
  "height": 200,
  "url": "https://obsidian.md"
}
```

### 组节点

组是用于组织其他节点的视觉容器。将子节点放置在组的边界内。

| 属性 | 必填 | 类型 | 描述 |
|-----------|----------|------|-------------|
| `label` | 否 | string | 组的文本标签 |
| `background` | 否 | string | 背景图像的路径 |
| `backgroundStyle` | 否 | string | `cover`、`ratio` 或 `repeat` |

```json
{
  "id": "d4e5f6789012345a",
  "type": "group",
  "x": -50,
  "y": -50,
  "width": 1000,
  "height": 600,
  "label": "Project Overview",
  "color": "4"
}
```

## 边

边通过 `fromNode` 和 `toNode` ID 连接节点。

| 属性 | 必填 | 类型 | 默认值 | 描述 |
|-----------|----------|------|---------|-------------|
| `id` | 是 | string | - | 唯一标识符 |
| `fromNode` | 是 | string | - | 源节点 ID |
| `fromSide` | 否 | string | - | `top`、`right`、`bottom` 或 `left` |
| `fromEnd` | 否 | string | `none` | `none` 或 `arrow` |
| `toNode` | 是 | string | - | 目标节点 ID |
| `toSide` | 否 | string | - | `top`、`right`、`bottom` 或 `left` |
| `toEnd` | 否 | string | `arrow` | `none` 或 `arrow` |
| `color` | 否 | canvasColor | - | 线条颜色 |
| `label` | 否 | string | - | 文本标签 |

```json
{
  "id": "0123456789abcdef",
  "fromNode": "6f0ad84f44ce9c17",
  "fromSide": "right",
  "toNode": "a1b2c3d4e5f67890",
  "toSide": "left",
  "toEnd": "arrow",
  "label": "leads to"
}
```

## 颜色

`canvasColor` 类型接受十六进制字符串或预设数字：

| 预设 | 颜色 |
|--------|-------|
| `"1"` | 红色 |
| `"2"` | 橙色 |
| `"3"` | 黄色 |
| `"4"` | 绿色 |
| `"5"` | 青色 |
| `"6"` | 紫色 |

预设颜色值是有意未定义的 —— 应用程序使用它们自己的品牌颜色。

## ID 生成

生成 16 字符小写十六进制字符串（64 位随机值）：

```
"6f0ad84f44ce9c17"
"a3b2c1d0e9f8a7b6"
```

## 布局指南

- 坐标可以为负（画布无限延伸）
- `x` 向右增加，`y` 向下增加；位置是左上角
- 节点间距 50-100px；组内留 20-50px 内边距
- 对齐网格（10 或 20 的倍数）以获得更整洁的布局

| 节点类型 | 建议宽度 | 建议高度 |
|-----------|-----------------|------------------|
| 小文本 | 200-300 | 80-150 |
| 中文本 | 300-450 | 150-300 |
| 大文本 | 400-600 | 300-500 |
| 文件预览 | 300-500 | 200-400 |
| 链接预览 | 250-400 | 100-200 |

## 验证清单

创建或编辑画布文件后，验证：

1. 所有 `id` 值在节点和边中都是唯一的
2. 每个 `fromNode` 和 `toNode` 都引用现有的节点 ID
3. 每种节点类型都存在必填字段（文本节点为 `text`，文件节点为 `file`，链接节点为 `url`）
4. `type` 为以下之一：`text`、`file`、`link`、`group`
5. `fromSide`/`toSide` 值为以下之一：`top`、`right`、`bottom`、`left`
6. `fromEnd`/`toEnd` 值为以下之一：`none`、`arrow`
7. 颜色预设为 `"1"` 到 `"6"` 或有效的十六进制（例如 `"#FF0000"`）
8. JSON 有效且可解析

如果验证失败，请检查是否有重复的 ID、悬空的边引用或格式错误的 JSON 字符串（尤其是文本内容中未转义的换行符）。

## 完整示例

请参阅 [references/EXAMPLES.md](references/EXAMPLES.md) 获取完整的画布示例，包括思维导图、项目看板、研究画布和流程图。

## 参考资料

- [JSON Canvas 规范 1.0](https://jsoncanvas.org/spec/1.0/)
- [JSON Canvas GitHub](https://github.com/obsidianmd/jsoncanvas)