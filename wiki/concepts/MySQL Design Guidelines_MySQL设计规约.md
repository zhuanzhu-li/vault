---
title: MySQL Design Guidelines（MySQL设计规约）
type: concept
tags: [java, mysql, database-design, coding-standards, alibaba]
sources:
  - "[2026-05-26-alibaba-java-development-manual](raw/clippings/2026-05-26-alibaba-java-development-manual.md)"
---

# MySQL Design Guidelines（MySQL设计规约）

MySQL设计规约是阿里巴巴Java开发手册中关于数据库设计的核心规范，涵盖建表规约、索引规约、SQL语句和ORM映射四个维度，旨在指导开发者设计出高效、可维护的数据库结构。

## 一、建表规约

### 字段设计原则

| 原则 | 说明 | 正例 |
|------|------|------|
| 布尔字段命名 | 使用 `is_xxx` 方式，数据类型为 `unsigned tinyint` | `is_deleted` (1删除/0未删除) |
| 命名规范 | 小写字母或数字，禁止数字开头 | `aliyun_admin`、`rdc_config` |
| 表名单数 | 不使用复数名词 | 表名对应DO类名也是单数形式 |
| 禁用保留字 | 如 `desc`、`range`、`match`、`delayed` | 参考MySQL官方保留字 |

### 索引命名规范

| 类型 | 命名格式 | 示例 |
|------|----------|------|
| 主键索引 | `pk_字段名` | `pk_id` |
| 唯一索引 | `uk_字段名` | `uk_name` |
| 普通索引 | `idx_字段名` | `idx_gmt_create` |

### 数据类型选择

| 场景 | 推荐类型 | 说明 |
|------|----------|------|
| 小数 | `decimal` | 禁止使用 `float`/`double`，避免精度损失 |
| 定长字符串 | `char` | 存储长度几乎相等的字符串 |
| 变长字符串 | `varchar` | 长度不超过5000，否则用 `text` 独立建表 |
| 主键 | `unsigned bigint` | 单表自增，步长为1 |

### 必须字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | `unsigned bigint` | 主键，自增 |
| `gmt_create` | `datetime` | 主动创建时间 |
| `gmt_modified` | `datetime` | 被动更新時間 |

## 二、索引规约

### 核心规则

| 规则 | 说明 |
|------|------|
| 唯一索引 | 业务上具有唯一特性的字段，即使是多字段组合，也必须建唯一索引 |
| 多表join限制 | 超过三个表禁止join，关联字段类型必须绝对一致 |
| 索引长度 | varchar字段建立索引时必须指定索引长度，根据区分度确定 |
| 左模糊禁止 | 页面搜索严禁左模糊或全模糊，必须走搜索引擎 |

### 索引优化技巧

| 技巧 | 说明 |
|------|------|
| **覆盖索引** | 用 `explain` 的 `extra` 列出现 `using index`，避免回表 |
| **最左前缀** | order by 最后的字段放在组合索引顺序最后 |
| **延迟关联** | 优化超多分页：先定位id段，再关联 |
| **区分度优先** | 建组合索引时，区分度最高的在最左边 |

## 三、SQL 语句 规约

| 规约 | 说明 |
|------|------|
| `count(*)` vs `count(1)` | `count(*)` 是 SQL92 定义的标准统计行数语法 |
| `count(列名)` | 不会统计值为 NULL 的行 |
| 分页优化 | 避免 `offset` 过大，采用延迟关联 |

## 四、ORM 映射 规约

| 规约 | 说明 |
|------|------|
| POJO类属性类型 | 与数据库字段类型对应，避免混淆 |
| 日志输出 | SQL日志级别至少为 `WARN` 或 `ERROR` |
| 禁用 `select *` | 必须指定字段列表 |

## Related

- [PostgreSQL_PostgreSQL](PostgreSQL_PostgreSQL.md)
- [MVCC_MVCC](MVCC_MVCC.md)
- [Code Quality Standards_代码质量规范](Code%20Quality%20Standards_代码质量规范.md)
