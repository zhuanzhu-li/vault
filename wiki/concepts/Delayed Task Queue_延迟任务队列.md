---
title: Delayed Task Queue（延迟任务队列）
type: concept
tags: [distributed-systems, message-queue, scheduling, architecture]
sources: []
---

# Delayed Task Queue（延迟任务队列）

延迟任务队列用于实现任务的定时/延迟执行，是分布式系统中处理异步延时任务的核心组件。

## 常见实现方案

| 方案 | 实现 | 精度 | 优点 | 缺点 |
|------|------|------|------|------|
| **JDK Timer** | 单线程 | 毫秒 | 简单 | 单点故障 |
| **ScheduledExecutor** | 线程池 | 毫秒 | 可多线程 | 无持久化 |
| **时间轮算法** | Hash 环 | 毫秒 | 高性能 | 实现复杂 |
| **Redis ZSet** | 有序集合 | 毫秒 | 简单 | 依赖 Redis |
| **RabbitMQ 延迟消息** | TTL + DLX | 秒级 | MQ 特性 | 精度较低 |
| **Kafka 延迟消息** | 延迟分区 | 秒级 | 高吞吐 | 配置复杂 |

## 时间轮算法

### 原理

```
┌─────────────────────────────────────────────────────┐
│                   时间轮 (Tick = 1秒)                │
├─────────────────────────────────────────────────────┤
│  0  │  1  │  2  │  3  │  4  │  5  │ ... │ 63 │
│  ●  │     │     │     │     │     │     │     │
│     │     │  ●  │     │     │     │     │     │
│     │     │     │     │  ●  │     │     │     │
├─────────────────────────────────────────────────────┤
│  当前指针指向 0，每秒转动一格                         │
└─────────────────────────────────────────────────────┘
```

### 层级时间轮

```
┌─────────────────────────────────────────────────┐
│                   小时轮 (Tick = 1小时)            │
│  ┌───┬───┬───┬───┐                             │
│  │ 0 │ 1 │ 2 │ 3│  →  指向天轮 (Tick = 1天)  │
│  └───┴───┴───┴───┘                             │
├─────────────────────────────────────────────────┤
│                   天轮 (Tick = 1天)               │
│  ┌───┬───┬───┬───┐                             │
│  │ 0 │ 1 │ 2 │ 3│  →  指向小时轮 (Tick = 1小时)│
│  └───┴───┴───┴───┘                             │
└─────────────────────────────────────────────────┘
```

### Netty HashedWheelTimer 示例

```java
public class DelayedTaskExample {
    public static void main(String[] args) {
        // 创建时间轮，每格 100ms，共 512 格
        HashedWheelTimer timer = new HashedWheelTimer(100, 
            TimeUnit.MILLISECONDS, 512);
        
        // 添加延迟任务：3秒后执行
        Timeout timeout = timer.newTimeout(() -> {
            System.out.println("任务执行!");
        }, 3, TimeUnit.SECONDS);
        
        // 取消任务
        if (!timeout.isExpired()) {
            timeout.cancel();
        }
    }
}
```

## Redis ZSet 延迟队列

### 原理

```
ZADD delayed:queue <timestamp> <task_data>
ZRANGEBYSCORE delayed:queue 0 <current_timestamp>

ZADD delayed:queue 1716800000 "order:123:expire"
ZADD delayed:queue 1716800010 "order:456:expire"
ZADD delayed:queue 1716800020 "order:789:expire"
```

### 实现代码

```java
@Component
public class RedisDelayQueue {
    
    @Autowired
    private StringRedisTemplate redisTemplate;
    
    private static final String DELAY_KEY = "delayed:queue";
    
    // 添加延迟任务
    public void addDelayTask(String taskId, long delayMillis) {
        long executeTime = System.currentTimeMillis() + delayMillis;
        redisTemplate.opsForZSet().add(DELAY_KEY, taskId, executeTime);
    }
    
    // 消费延迟任务
    public String pollTask() {
        long now = System.currentTimeMillis();
        // 获取已到期的任务（分数 <= 当前时间）
        Set<String> tasks = redisTemplate.opsForZSet()
            .rangeByScore(DELAY_KEY, 0, now);
        
        if (tasks == null || tasks.isEmpty()) {
            return null;
        }
        
        for (String taskId : tasks) {
            // 原子性删除（防止重复消费）
            Long removed = redisTemplate.opsForZSet()
                .remove(DELAY_KEY, taskId);
            if (removed != null && removed > 0) {
                return taskId;
            }
        }
        return null;
    }
}
```

### 轮询消费

```java
@Scheduled(fixedRate = 100)
public void consumeDelayTasks() {
    String taskId;
    while ((taskId = pollTask()) != null) {
        try {
            processTask(taskId);
        } catch (Exception e) {
            // 失败重试或放入死信队列
            handleFailure(taskId, e);
        }
    }
}
```

## RabbitMQ 延迟消息

### TTL + Dead Letter Exchange

```
┌──────────────┐     TTL     ┌──────────────┐
│   延迟队列    │──────────▶│  死信队列     │
│ delay.queue  │ 过期后进入  │ dlx.exchange │
└──────────────┘            └──────────────┘
         │                           │
         │                           ▼
         │                   ┌──────────────┐
         └──────────────────▶│  消费者       │
                             └──────────────┘
```

### 配置

```yaml
spring:
  rabbitmq:
    listener:
      simple:
        acknowledge-mode: manual
    
    bindings:
      delay-queue:
        queue-arguments:
          x-dead-letter-exchange: dlx.exchange
          x-message-ttl: 30000  # 30秒延迟
```

### 发送延迟消息

```java
public void sendDelayMessage(String msg, int delaySeconds) {
    rabbitTemplate.convertAndSend(
        "delay.exchange",    # 交换机
        "delay.routing.key", # 路由键
        msg,                 # 消息体
        message -> {
            message.getMessageProperties()
                .setExpiration(String.valueOf(delaySeconds * 1000));
            return message;
        }
    );
}
```

## 常见使用场景

| 场景 | 延迟时间 | 实现建议 |
|------|---------|---------|
| **订单超时取消** | 15-30分钟 | Redis ZSet / RabbitMQ |
| **短信/邮件延迟发送** | 几秒~几分钟 | RabbitMQ / 定时任务 |
| **优惠券过期提醒** | 几天 | 定时扫描数据库 |
| **接口重试** | 秒级 | 定时任务 + 内存队列 |
| **心跳检测** | 秒级 | 时间轮 |
| **缓存预热** | 分钟级 | 定时任务 |

## 选型建议

| 要求 | 推荐方案 |
|------|---------|
| **毫秒级精度** | 时间轮算法（Netty HashedWheelTimer） |
| **简单实现** | Redis ZSet |
| **已有 RabbitMQ** | RabbitMQ 延迟消息 |
| **已有 Kafka** | Kafka 延迟分区 |
| **秒级精度** | RabbitMQ / 定时任务扫描 |
| **持久化需求** | RabbitMQ / Redis 持久化 |

## 相关

- [Distributed Lock_分布式锁](Distributed%20Lock_分布式锁.md)
- [Distributed Transaction_分布式事务](Distributed%20Transaction_分布式事务.md)
