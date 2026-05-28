---
title: Seckill System Architecture（秒杀系统架构）
type: concept
tags: [high-concurrency, seckill, architecture, distributed-systems, redis]
sources: []
---

# Seckill System Architecture（秒杀系统架构）

秒杀系统是电商场景中最具挑战性的高并发场景之一，核心难点在于：瞬间流量巨大（峰值 QPS 可能达到几十万）、库存有限、超卖问题。秒杀系统的设计体现了分布式系统各个知识点综合应用。

## 秒杀系统挑战

| 挑战 | 描述 |
|------|------|
| **瞬时高并发** | 活动开始瞬间，QPS 可能是平时的 100 倍 |
| **库存有限** | 商品数量有限，不能超卖 |
| **一致性问题** | 库存扣减不能多也不能少 |
| **系统可用性** | 不能因为秒杀影响其他业务 |

## 秒杀系统架构图

```
┌─────────────────────────────────────────────────────────────────────┐
│                          用户端                                      │
│                    APP / Web / 小程序                               │
└────────────────────────────┬────────────────────────────────────────┘
                             │ HTTPS
                             ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         CDN / 静态资源                               │
└────────────────────────────┬────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       负载均衡层 (SLB)                              │
│                    Nginx / 云厂商 LB                                │
└────────────────────────────┬────────────────────────────────────────┘
                             │
        ┌────────────────────┼────────────────────┐
        ▼                    ▼                    ▼
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│  秒杀网关     │    │  秒杀网关     │    │  秒杀网关     │
│  (限流)      │    │  (限流)      │    │  (限流)      │
└──────┬───────┘    └──────┬───────┘    └──────┬───────┘
        │                    │                    │
        └────────────────────┼────────────────────┘
                             ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        秒杀服务层                                   │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐           │
│  │ 活动服务    │    │ 订单服务    │    │ 库存服务    │           │
│  └──────┬─────┘    └──────┬─────┘    └──────┬─────┘           │
└─────────┼──────────────────┼──────────────────┼─────────────────────┘
          │                  │                  │
          ▼                  ▼                  ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         缓存层                                       │
│  ┌──────────────────────────────────────────────────────┐         │
│  │              Redis Cluster                              │         │
│  │  活动缓存  │  库存缓存  │  Token 验证  │  限流计数  │         │
│  └──────────────────────────────────────────────────────┘         │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         消息队列                                     │
│  ┌──────────────┐    ┌──────────────┐                            │
│  │ RocketMQ     │    │ Kafka         │                            │
│  │ (订单消息)    │    │ (日志分析)   │                            │
│  └──────────────┘    └──────────────┘                            │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         数据库层                                     │
│  ┌──────────────┐    ┌──────────────┐                            │
│  │  MySQL 主库   │    │  MySQL 从库   │                            │
│  │  (订单/库存)  │    │  (查询)       │                            │
│  └──────────────┘    └──────────────┘                            │
└─────────────────────────────────────────────────────────────────────┘
```

## 核心设计要点

### 1. 流量削峰

| 策略 | 说明 | 实现 |
|------|------|------|
| **验证码** | 答题/滑动验证，过滤非正常流量 | 活动前 5 分钟开始 |
| **抢购按钮** | 活动开始前按钮置灰，防止提前刷 | 前端控制 |
| **分批次放行** | 不同 UID 段分不同时段进入 | Redis 队列 |
| **MQ 异步下单** | 抢到名额后异步创建订单 | RocketMQ |

### 2. 库存扣减

#### Redis 原子扣减（Lua 脚本）

```lua
-- seckill.lua
local stock_key = KEYS[1]
local user_key = KEYS[2]
local quantity = tonumber(ARGV[1])

-- 检查用户是否已购买
if redis.call('SISMEMBER', user_key, ARGV[3]) == 1 then
    return -1  -- 已购买
end

-- 检查库存
local stock = tonumber(redis.call('GET', stock_key))
if stock < quantity then
    return -2  -- 库存不足
end

-- 扣减库存
redis.call('DECRBY', stock_key, quantity)
-- 标记用户已购买
redis.call('SADD', user_key, ARGV[3])

return stock - quantity  -- 返回剩余库存
```

#### Java 调用

```java
public Result seckill(Long userId, Long productId) {
    String stockKey = "seckill:stock:" + productId;
    String userKey = "seckill:user:" + productId;
    
    String result = redisTemplate.execute(
        new DefaultRedisScript<>(script, Long.class),
        Arrays.asList(stockKey, userKey),
        1, userId.toString()
    );
    
    if (result == null) {
        return Result.error("系统繁忙");
    }
    if (result == -1) {
        return Result.error("已购买过");
    }
    if (result == -2) {
        return Result.error("库存不足");
    }
    
    // 发送订单消息
    orderProducer.sendOrderMessage(userId, productId);
    
    return Result.success("抢购成功");
}
```

### 3. MQ 异步下单

```java
@RocketMQMessageListener(topic = "seckill-order", consumerGroup = "order-consumer")
public class OrderConsumer {
    
    @Autowired
    private OrderService orderService;
    
    public void onMessage(String message) {
        OrderMessage msg = JSON.parseObject(message, OrderMessage.class);
        
        try {
            orderService.createOrder(msg.getUserId(), msg.getProductId());
        } catch (DuplicateOrderException e) {
            // 幂等处理，恢复库存
            redisTemplate.opsForValue().increment(
                "seckill:stock:" + msg.getProductId());
        }
    }
}
```

### 4. 限流策略

| 策略 | 说明 | 实现 |
|------|------|------|
| **接口限流** | 限制进入秒杀接口的 QPS | Sentinel |
| **用户限流** | 限制同一用户抢购频率 | Redis计数 |
| **活动限流** | 限制活动总抢购次数 | Redis原子扣减 |

```java
// 用户维度限流：每个用户每秒最多抢 1 次
public boolean tryAcquire(Long userId, Long activityId) {
    String key = "rate:user:" + userId + ":" + activityId;
    Boolean success = redisTemplate.opsForValue()
        .setIfAbsent(key, "1", 1, TimeUnit.SECONDS);
    return Boolean.TRUE.equals(success);
}
```

### 5. 库存回滚

| 场景 | 处理方式 |
|------|---------|
| **MQ 消费失败** | 重试 3 次，失败后补偿库存 |
| **订单超时未支付** | 解锁库存，恢复 Redis 和 DB |
| **用户主动取消** | 解锁库存 |

```java
// 解锁库存
public void releaseStock(Long productId, Long quantity) {
    String key = "seckill:stock:" + productId;
    redisTemplate.opsForValue().increment(key, quantity);
    
    // 记录解锁日志，用于对账
    redisTemplate.opsForList().leftPush(
        "seckill:release:log", 
        JSON.toJSONString(new StockRelease(productId, quantity, System.currentTimeMillis()))
    );
}
```

## 防刷设计

| 策略 | 说明 |
|------|------|
| **设备指纹** | 识别模拟器/虚拟机 |
| **行为分析** | 正常用户操作间隔 > 1 秒 |
| **IP 黑名单** | 限制高频 IP |
| **风控系统** | 接入风控服务 |
| **验证码** | 人机识别 |

## 关键监控指标

| 指标 | 告警阈值 |
|------|---------|
| **QPS** | 超过预期峰值 80% |
| **成功率** | 低于 99% |
| **响应时间** | P99 > 500ms |
| **错误率** | > 1% |
| **库存对账** | 记录与实际不一致 |

## 相关

- [Distributed Lock_分布式锁](Distributed%20Lock_分布式锁.md)
- [Interface Idempotency Design_接口幂等设计](Interface%20Idempotency%20Design_接口幂等设计.md)
- [Cache Architecture Design_缓存架构设计](Cache%20Architecture%20Design_缓存架构设计.md)
- [Distributed Transaction_分布式事务](Distributed%20Transaction_分布式事务.md)
