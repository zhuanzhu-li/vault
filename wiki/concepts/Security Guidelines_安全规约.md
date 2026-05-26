---
title: Security Guidelines（安全规约）
type: concept
tags: [java, security, coding-standards, alibaba]
sources:
  - "[2026-05-26-alibaba-java-development-manual](raw/clippings/2026-05-26-alibaba-java-development-manual.md)"
---

# Security Guidelines（安全规约）

安全规约是保护系统免受恶意攻击的重要规范，涵盖权限控制、数据脱敏、输入校验、SQL注入防护、CSRF防护等多个维度。

## 一、权限控制

**强制**：隶属于用户个人的页面或功能必须进行权限控制校验。

**目的**：防止水平权限攻击——用户可以随意访问、修改、删除其他用户的数据。

| 攻击场景 | 防护措施 |
|---------|---------|
| 查看他人私信 | 校验当前用户与资源所属关系 |
| 修改他人订单 | 校验订单归属 |
| 删除他人数据 | 校验数据权限 |

## 二、数据脱敏

**强制**：用户敏感数据禁止直接展示，必须进行脱敏处理。

| 数据类型 | 脱敏方式 | 示例 |
|---------|---------|------|
| 手机号码 | 隐藏中间4位 | `158****9119` |
| 身份证号 | 隐藏出生年月日 | `330***********1234` |
| 邮箱 | 显示前两位+域名 | `ab***@example.com` |
| 银行卡 | 显示后4位 | `****1234` |

## 三、SQL 注入防护

**强制**：用户请求的SQL参数必须使用参数绑定，禁止字符串拼接SQL。

```java
// 正确：使用参数绑定
PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
ps.setString(1, userId);

// 错误：字符串拼接
String sql = "SELECT * FROM users WHERE id = " + userId;
```

## 四、输入校验

**强制**：用户请求传入的任何参数必须做有效性验证。

| 风险 | 后果 | 防护措施 |
|------|------|---------|
| page size 过大 | 内存溢出 | 限制最大值 |
| 恶意 order by | 数据库慢查询 | 白名单校验 |
| 正则 ReDoS | 服务拒绝 | 避免贪婪匹配，使用非贪婪 |
| SQL注入 | 数据泄露 | 参数绑定 |
| 反序列化注入 | 远程代码执行 | 校验输入来源 |

## 五、CSRF 防护

**强制**：表单、AJAX 提交必须执行 CSRF 安全过滤。

**CSRF 原理**：攻击者构造URL，受害者访问后自动执行恶意操作。

| 防护方式 | 说明 |
|---------|------|
| Token 校验 | 在表单中使用随机 Token |
| 验证码 | 人机识别 |
| Referer 校验 | 校验请求来源 |

## 六、防刷与风控

**强制**：在使用短信、邮件、电话、下单、支付等功能时，必须实现防重放限制。

| 限制类型 | 实现方式 |
|---------|---------|
| 频率限制 | 单位时间内最大请求次数 |
| 疲劳度控制 | 超过阈值后要求验证码 |
| 验证码校验 | 人机识别 |

## Related

- [Code Quality Standards_代码质量规范](Code%20Quality%20Standards_代码质量规范.md)
- [MySQL Design Guidelines_MySQL设计规约](MySQL%20Design%20Guidelines_MySQL设计规约.md)
- [Exception Handling Standards_异常处理规范](Exception%20Handling%20Standards_异常处理规范.md)
