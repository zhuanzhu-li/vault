---
name: obsidian-bases
description: 创建和编辑 Obsidian Bases（.base 文件），包含视图、过滤器、公式和摘要。在处理 .base 文件、创建笔记的类似数据库视图，或用户在 Obsidian 中提到 Bases、表格视图、卡片视图、过滤器或公式时使用。
---

# Obsidian Bases 技能

## 工作流

1. **创建文件**：在 vault 中创建一个包含有效 YAML 内容的 `.base` 文件
2. **定义范围**：添加 `filters` 以选择显示哪些笔记（按标签、文件夹、属性或日期）
3. **添加公式**（可选）：在 `formulas` 部分定义计算属性
4. **配置视图**：添加一个或多个视图（`table`、`cards`、`list` 或 `map`），并使用 `order` 指定要显示的属性
5. **验证**：验证文件是有效的 YAML，没有语法错误。检查所有引用的属性和公式是否存在。常见问题：未加引号的字符串包含特殊 YAML 字符，公式表达式中引号不匹配，引用 `formula.X` 但未在 `formulas` 中定义 `X`
6. **在 Obsidian 中测试**：在 Obsidian 中打开 `.base` 文件以确认视图渲染正确。如果显示 YAML 错误，请检查下面的引号规则

## Schema

Base 文件使用 `.base` 扩展名并包含有效的 YAML。

```yaml
# 全局过滤器适用于 base 中的所有视图
filters:
  # 可以是单个过滤器字符串
  # 或带有 and/or/not 的递归过滤器对象
  and: []
  or: []
  not: []

# 定义可在所有视图中使用的公式属性
formulas:
  formula_name: 'expression'

# 配置属性的显示名称和设置
properties:
  property_name:
    displayName: "显示名称"
  formula.formula_name:
    displayName: "公式显示名称"
  file.ext:
    displayName: "扩展名"

# 定义自定义摘要公式
summaries:
  custom_summary_name: 'values.mean().round(3)'

# 定义一个或多个视图
views:
  - type: table | cards | list | map
    name: "视图名称"
    limit: 10                    # 可选：限制结果数量
    groupBy:                     # 可选：对结果进行分组
      property: property_name
      direction: ASC | DESC
    filters:                     # 视图特定的过滤器
      and: []
    order:                       # 按顺序显示的属性
      - file.name
      - property_name
      - formula.formula_name
    summaries:                   # 将属性映射到摘要公式
      property_name: Average
```

## 过滤器语法

过滤器用于缩小结果范围。它们可以全局应用或按视图应用。

### 过滤器结构

```yaml
# 单个过滤器
filters: 'status == "done"'

# AND - 所有条件必须为真
filters:
  and:
    - 'status == "done"'
    - 'priority > 3'

# OR - 任何条件都可以为真
filters:
  or:
    - 'file.hasTag("book")'
    - 'file.hasTag("article")'

# NOT - 排除匹配的项
filters:
  not:
    - 'file.hasTag("archived")'

# 嵌套过滤器
filters:
  or:
    - file.hasTag("tag")
    - and:
        - file.hasTag("book")
        - file.hasLink("Textbook")
    - not:
        - file.hasTag("book")
        - file.inFolder("Required Reading")
```

### 过滤器运算符

| 运算符 | 描述 |
|----------|-------------|
| `==` | 等于 |
| `!=` | 不等于 |
| `>` | 大于 |
| `<` | 小于 |
| `>=` | 大于或等于 |
| `<=` | 小于或等于 |
| `&&` | 逻辑与 |
| `\|\|` | 逻辑或 |
| `!` | 逻辑非 |

## 属性

### 三种类型的属性

1. **笔记属性** - 来自 frontmatter：`note.author` 或直接 `author`
2. **文件属性** - 文件元数据：`file.name`、`file.mtime` 等
3. **公式属性** - 计算值：`formula.my_formula`

### 文件属性参考

| 属性 | 类型 | 描述 |
|----------|------|-------------|
| `file.name` | String | 文件名 |
| `file.basename` | String | 不带扩展名的文件名 |
| `file.path` | String | 文件的完整路径 |
| `file.folder` | String | 父文件夹路径 |
| `file.ext` | String | 文件扩展名 |
| `file.size` | Number | 文件大小（字节） |
| `file.ctime` | Date | 创建时间 |
| `file.mtime` | Date | 修改时间 |
| `file.tags` | List | 文件中的所有标签 |
| `file.links` | List | 文件中的内部链接 |
| `file.backlinks` | List | 链接到此文件的文件 |
| `file.embeds` | List | 笔记中的嵌入 |
| `file.properties` | Object | 所有 frontmatter 属性 |

### `this` 关键字

- 在主内容区域：指 base 文件本身
- 嵌入时：指嵌入的文件
- 在侧边栏中：指主内容中的活动文件

## 公式语法

公式从属性计算值。在 `formulas` 部分定义。

```yaml
formulas:
  # 简单算术
  total: "price * quantity"

  # 条件逻辑
  status_icon: 'if(done, "✅", "⏳")'

  # 字符串格式化
  formatted_price: 'if(price, price.toFixed(2) + " dollars")'

  # 日期格式化
  created: 'file.ctime.format("YYYY-MM-DD")'

  # 计算创建以来的天数（使用 .days 获取 Duration）
  days_old: '(now() - file.ctime).days'

  # 计算距离截止日期的天数
  days_until_due: 'if(due_date, (date(due_date) - today()).days, "")'
```

## 关键函数

最常用的函数。有关所有类型（Date、String、Number、List、File、Link、Object、RegExp）的完整参考，请参阅 [FUNCTIONS_REFERENCE.md](references/FUNCTIONS_REFERENCE.md)。

| 函数 | 签名 | 描述 |
|----------|-----------|-------------|
| `date()` | `date(string): date` | 将字符串解析为日期（`YYYY-MM-DD HH:mm:ss`） |
| `now()` | `now(): date` | 当前日期和时间 |
| `today()` | `today(): date` | 当前日期（时间 = 00:00:00） |
| `if()` | `if(condition, trueResult, falseResult?)` | 条件 |
| `duration()` | `duration(string): duration` | 解析持续时间字符串 |
| `file()` | `file(path): file` | 获取文件对象 |
| `link()` | `link(path, display?): Link` | 创建链接 |

### Duration 类型

当减去两个日期时，结果是 **Duration** 类型（不是数字）。

**Duration 字段：** `duration.days`、`duration.hours`、`duration.minutes`、`duration.seconds`、`duration.milliseconds`

**重要提示：** Duration 不直接支持 `.round()`、`.floor()`、`.ceil()`。首先访问数字字段（如 `.days`），然后应用数字函数。

```yaml
# 正确：计算日期之间的天数
"(date(due_date) - today()).days"                    # 返回天数
"(now() - file.ctime).days"                          # 创建以来的天数
"(date(due_date) - today()).days.round(0)"           # 四舍五入的天数

# 错误 - 会导致错误：
# "((date(due) - today()) / 86400000).round(0)"      # Duration 不支持除法然后四舍五入
```

### 日期算术

```yaml
# 持续时间单位：y/year/years, M/month/months, d/day/days,
#                 w/week/weeks, h/hour/hours, m/minute/minutes, s/second/seconds
"now() + \"1 day\""       # 明天
"today() + \"7d\""        # 从今天起一周
"now() - file.ctime"      # 返回 Duration
"(now() - file.ctime).days"  # 获取天数作为数字
```

## 视图类型

### 表格视图

```yaml
views:
  - type: table
    name: "我的表格"
    order:
      - file.name
      - status
      - due_date
    summaries:
      price: Sum
      count: Average
```

### 卡片视图

```yaml
views:
  - type: cards
    name: "画廊"
    order:
      - file.name
      - cover_image
      - description
```

### 列表视图

```yaml
views:
  - type: list
    name: "简单列表"
    order:
      - file.name
      - status
```

### 地图视图

需要经度/纬度属性和 Maps 社区插件。

```yaml
views:
  - type: map
    name: "位置"
    # lat/lng 属性的地图特定设置
```

## 默认摘要公式

| 名称 | 输入类型 | 描述 |
|------|------------|-------------|
| `Average` | Number | 数学平均值 |
| `Min` | Number | 最小值 |
| `Max` | Number | 最大值 |
| `Sum` | Number | 所有数字的总和 |
| `Range` | Number | 最大值 - 最小值 |
| `Median` | Number | 数学中位数 |
| `Stddev` | Number | 标准差 |
| `Earliest` | Date | 最早日期 |
| `Latest` | Date | 最晚日期 |
| `Range` | Date | 最晚 - 最早 |
| `Checked` | Boolean | true 值的计数 |
| `Unchecked` | Boolean | false 值的计数 |
| `Empty` | Any | 空值的计数 |
| `Filled` | Any | 非空值的计数 |
| `Unique` | Any | 唯一值的计数 |

## 完整示例

### 任务追踪 Base

```yaml
filters:
  and:
    - file.hasTag("task")
    - 'file.ext == "md"'

formulas:
  days_until_due: 'if(due, (date(due) - today()).days, "")'
  is_overdue: 'if(due, date(due) < today() && status != "done", false)'
  priority_label: 'if(priority == 1, "🔴 High", if(priority == 2, "🟡 Medium", "🟢 Low"))'

properties:
  status:
    displayName: 状态
  formula.days_until_due:
    displayName: "距离截止天数"
  formula.priority_label:
    displayName: 优先级

views:
  - type: table
    name: "活动任务"
    filters:
      and:
        - 'status != "done"'
    order:
      - file.name
      - status
      - formula.priority_label
      - due
      - formula.days_until_due
    groupBy:
      property: status
      direction: ASC
    summaries:
      formula.days_until_due: Average

  - type: table
    name: "已完成"
    filters:
      and:
        - 'status == "done"'
    order:
      - file.name
      - completed_date
```

### 阅读列表 Base

```yaml
filters:
  or:
    - file.hasTag("book")
    - file.hasTag("article")

formulas:
  reading_time: 'if(pages, (pages * 2).toString() + " min", "")'
  status_icon: 'if(status == "reading", "📖", if(status == "done", "✅", "📚"))'
  year_read: 'if(finished_date, date(finished_date).year, "")'

properties:
  author:
    displayName: 作者
  formula.status_icon:
    displayName: ""
  formula.reading_time:
    displayName: "预计时间"

views:
  - type: cards
    name: "图书馆"
    order:
      - cover
      - file.name
      - author
      - formula.status_icon
    filters:
      not:
        - 'status == "dropped"'

  - type: table
    name: "阅读列表"
    filters:
      and:
        - 'status == "to-read"'
    order:
      - file.name
      - author
      - pages
      - formula.reading_time
```

### 每日笔记索引

```yaml
filters:
  and:
    - file.inFolder("Daily Notes")
    - '/^\d{4}-\d{2}-\d{2}$/.matches(file.basename)'

formulas:
  word_estimate: '(file.size / 5).round(0)'
  day_of_week: 'date(file.basename).format("dddd")'

properties:
  formula.day_of_week:
    displayName: "星期"
  formula.word_estimate:
    displayName: "~字数"

views:
  - type: table
    name: "最近笔记"
    limit: 30
    order:
      - file.name
      - formula.day_of_week
      - formula.word_estimate
      - file.mtime
```

## 嵌入 Bases

嵌入在 Markdown 文件中：

```markdown
![[MyBase.base]]

<!-- 特定视图 -->
![[MyBase.base#View Name]]
```

## YAML 引号规则

- 对于包含双引号的公式，使用单引号：`'if(done, "Yes", "No")'`
- 对于简单字符串，使用双引号：`"My View Name"`
- 在复杂表达式中正确转义嵌套引号

## 故障排除

### YAML 语法错误

**未加引号的特殊字符**：包含 `:`、`{`、`}`、`[`、`]`、`,`、`&`、`*`、`#`、`?`、`|`、`-`、`<`、`>`、`=`、`!`、`%`、`@`、`` ` `` 的字符串必须加引号。

```yaml
# 错误 - 未加引号的字符串中有冒号
displayName: Status: Active

# 正确
displayName: "Status: Active"
```

**公式中引号不匹配**：当公式包含双引号时，用单引号包裹整个公式。

```yaml
# 错误 - 双引号内有双引号
formulas:
  label: "if(done, "Yes", "No")"

# 正确 - 单引号包裹双引号
formulas:
  label: 'if(done, "Yes", "No")'
```

### 常见公式错误

**没有字段访问的 Duration 数学运算**：减去日期返回的是 Duration，而不是数字。始终访问 `.days`、`.hours` 等。

```yaml
# 错误 - Duration 不是数字
"(now() - file.ctime).round(0)"

# 正确 - 先访问 .days，然后四舍五入
"(now() - file.ctime).days.round(0)"
```

**缺少 null 检查**：属性可能并非在所有笔记上都存在。使用 `if()` 进行保护。

```yaml
# 错误 - 如果 due_date 为空则崩溃
"(date(due_date) - today()).days"

# 正确 - 使用 if() 保护
'if(due_date, (date(due_date) - today()).days, "")'
```

**引用未定义的公式**：确保 `order` 或 `properties` 中的每个 `formula.X` 在 `formulas` 中都有匹配的条目。

```yaml
# 如果 'total' 未在 formulas 中定义，这将静默失败
order:
  - formula.total

# 修复：定义它
formulas:
  total: "price * quantity"
```

## 参考资料

- [Bases 语法](https://help.obsidian.md/bases/syntax)
- [函数](https://help.obsidian.md/bases/functions)
- [视图](https://help.obsidian.md/bases/views)
- [公式](https://help.obsidian.md/formulas)
- [完整函数参考](references/FUNCTIONS_REFERENCE.md)