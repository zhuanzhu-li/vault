---
title: Interface Idempotency Design（接口幂等设计）
type: concept
tags: [api-design, distributed-systems, architecture, java]
sources: []
---

# Interface Idempotency Design（接口幂等设计）

接口幂等性是指接口被多次调用时，返回结果与调用次数无关的特性。在分布式系统中，网络重试、消息重复消费等场景下，接口幂等性是保证数据一致性的关键。

## 为什么需要幂等

| 场景 | 问题 |
|------|------|
| **用户双击按钮** | 重复提交订单 |
| **网络超时重试** | 同一个请求发送多次 |
| **MQ 消息重复** | 消息被消费多次 |
| **定时任务重复执行** | 任务执行完成但状态未更新 |

## 幂等性保障方案

### 一、唯一 Token 机制

#### 流程

```
1. 客户端请求获取 Token
   GET /api/token → { token: "uuid-xxx" }

2. 客户端携带 Token 发起业务请求
   POST /api/order
   {
     "token": "uuid-xxx",
     "amount": 100
   }

3. 服务端检查 Token 是否已使用
   - 未使用：执行业务，标记 Token 已用
   - 已使用：直接返回成功（不重复执行业务）
```

#### Redis 实现

```java
public boolean checkToken(String token) {
    // SETNX + EXPIRE 原子操作
    String key = "idempotent:token:" + token;
    Boolean success = redisTemplate.opsForValue()
        .setIfAbsent(key, "1", 24, TimeUnit.HOURS);
    return Boolean.TRUE.equals(success);
}
```

#### 优缺点

| 优点 | 缺点 |
|------|------|
| 实现简单 | 需要额外获取 Token 的网络开销 |
| 通用性强 | Token 有效期管理复杂 |
| 可追溯 | 存储成本（短期可接受） |

### 二、唯一索引/约束

#### 数据库防重

```sql
-- 订单表设计
CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    order_no VARCHAR(64) UNIQUE NOT NULL,  -- 业务唯一键
    user_id BIGINT NOT NULL,
    amount DECIMAL(10,2),
    status TINYINT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 幂等插入
INSERT INTO orders (id, order_no, user_id, amount)
VALUES (1, 'ORDER_2024_001', 100, 100.00)
ON DUPLICATE KEY UPDATE id=id;  -- 已存在则忽略
```

#### 防重表

```sql
-- 幂等控制表
CREATE TABLE idempotent_log (
    biz_key VARCHAR(128) PRIMARY KEY,  -- 业务唯一键
    status TINYINT DEFAULT 0,          -- 0:处理中 1:成功
    result TEXT,                        -- 处理结果
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### 三、乐观锁

#### 版本号控制

```java
// 更新时检查版本
UPDATE account
SET balance = balance - #{amount},
    version = version + 1
WHERE user_id = #{userId}
  AND version = #{currentVersion}
  AND balance >= #{amount};  -- 余额足够
```

#### 影响行数判断

```java
int rows = jdbcTemplate.update(
    "UPDATE orders SET status = 1 WHERE id = ? AND status = 0",
    orderId
);
if (rows == 0) {
    throw new BusinessException("订单状态已变更，请勿重复提交");
}
```

### 四、分布式锁

```java
public Result createOrder(OrderDTO dto) {
    String lockKey = "order:idempotent:" + dto.getIdempotentKey();
    
    try {
        RLock lock = redissonClient.getLock(lockKey);
        boolean locked = lock.tryLock(5, 10, TimeUnit.SECONDS);
        
        if (!locked) {
            throw new BusinessException("系统繁忙，请稍后重试");
        }
        
        // 业务逻辑
        return doCreateOrder(dto);
    } finally {
        lock.unlock();
    }
}
```

## 不同场景的幂等策略

| 场景 | 推荐方案 |
|------|---------|
| **用户下单** | 唯一 Token + 订单号唯一索引 |
| **支付回调** | 支付流水号唯一索引 |
| **消息消费** | 消息 ID + 业务状态判断 |
| **定时任务** | 任务执行 ID + 分布式锁 |
| **数据更新** | 乐观锁（版本号/状态判断） |
| **文件上传** | 文件 MD5 + 存储唯一键 |

## 幂等性设计原则

| 原则 | 说明 |
|------|------|
| **天然幂等优先** | DELETE（删一次和删多次结果一样）、GET |
| **业务判断** | 状态变更前先检查当前状态 |
| **唯一键约束** | 数据库唯一索引是最后防线 |
| **日志可查** | 记录重复请求的日志便于排查 |
| **不返回错误** | 幂等接口对重复请求返回成功而非报错 |

## HTTP 语义与幂等

| 方法 | 幂等性 | 说明 |
|------|--------|------|
| GET | ✓ 幂等 | 读取资源，不改变状态 |
| HEAD | ✓ 幂等 | 与 GET 类似 |
| PUT | ✓ 幂等 | 替换资源，重复执行结果相同 |
| DELETE | ✓ 幂等 | 删除资源，重复删除无影响 |
| POST | ✗ 非幂等 | 创建资源，每次创建新资源 |
| PATCH | ? 可能幂等 | 取决于实现 |

## 相关

- [Distributed Lock_分布式锁](Distributed%20Lock_分布式锁.md)
- [MySQL Design Guidelines_MySQL设计规约](MySQL%20Design%20Guidelines_MySQL设计规约.md)
- [Distributed Transaction_分布式事务](Distributed%20Transaction_分布式事务.md)
