---
title: Service Governance（服务治理）
type: concept
tags: [microservices, service-mesh, architecture, distributed-systems]
sources: []
---

# Service Governance（服务治理）

服务治理是分布式系统和微服务架构中的核心概念，涵盖服务发现、负载均衡、熔断降级、限流、网关等能力，是保障系统稳定性和可用性的关键。

## 服务治理全景图

```
┌─────────────────────────────────────────────────────────────┐
│                      服务治理能力                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐     │
│  │服务发现 │  │负载均衡 │  │熔断降级 │  │限流    │     │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘     │
│                                                             │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐     │
│  │网关路由 │  │配置管理 │  │链路追踪 │  │安全    │     │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## 熔断器（Circuit Breaker）

### 原理

```
        正常状态              熔断打开            半开状态
    ┌────────────┐      ┌────────────┐      ┌────────────┐
    │            │      │            │      │            │
    │   CLOSED   │ ───▶ │    OPEN    │ ───▶ │ HALF_OPEN  │
    │   正常调用  │ 失败  │  快速失败  │ 超时  │  允许一个  │
    │            │ 次数  │   10秒后   │  请求  │  请求通过  │
    │            │ 超阈值│  自动进入  │ 通过  │            │
    └────────────┘      └────────────┘      └────────────┘
```

### 三种状态

| 状态 | 说明 | 行为 |
|------|------|------|
| **Closed** | 熔断器关闭 | 正常调用，监控失败率 |
| **Open** | 熔断器打开 | 快速失败，不调用下游 |
| **Half-Open** | 半开状态 | 允许一个请求通过，试探是否恢复 |

### 参数配置

| 参数 | 说明 | 典型值 |
|------|------|--------|
| **失败阈值** | 失败率达到此值触发熔断 | 50% |
| **快照窗口** | 统计失败率的时间窗口 | 10秒 |
| **熔断时长** | 熔断打开后多久进入半开 | 60秒 |
| **半开请求数** | 半开状态下允许通过的请求数 | 3 |

### Sentinel 实现

```java
@RestController
public class OrderController {
    
    @GetMapping("/order/{id}")
    @SentinelResource(value = "order",
        blockHandler = "orderBlock",
        fallback = "orderFallback")
    public Order getOrder(@PathVariable Long id) {
        return orderService.getOrder(id);
    }
    
    // 限流/熔断处理
    public Order orderBlock(Long id, BlockException e) {
        return Order.fallback("系统繁忙，请稍后重试");
    }
    
    // 降级处理
    public Order orderFallback(Long id, Throwable t) {
        return Order.fallback("服务暂时不可用");
    }
}
```

## 限流（Rate Limiting）

### 算法对比

| 算法 | 原理 | 优点 | 缺点 |
|------|------|------|------|
| **计数器** | 固定时间窗口计数 | 简单 | 边界突发 |
| **滑动窗口** | 滚动时间窗口 | 精确 | 实现复杂 |
| **漏桶** | 固定速率消费 | 流量平滑 | 可能丢请求 |
| **令牌桶** | 令牌以固定速率生成 | 支持突发 | 实现稍复杂 |

### 滑动窗口算法

```
时间窗口：1分钟，分成3个格子，每格20秒

t1(20s) │ t2(20s) │ t3(20s) │ t4(20s) ← 当前
当前请求计数 = t2 + t3 + t4
```

### 限流维度

| 维度 | 示例 | 应用场景 |
|------|------|---------|
| **单机限流** | 每秒1000请求 | 保护本机资源 |
| **全局限流** | 整个系统每秒10000请求 | 保护下游服务 |
| **用户维度** | 每个用户每分钟100请求 | 防止用户滥用 |
| **接口维度** | 每个接口单独限流 | 保护热点接口 |

### 限流实现示例

```java
// 基于 Redis 的滑动窗口限流
public boolean tryAcquire(String key, int limit, int windowSeconds) {
    String now = String.valueOf(System.currentTimeMillis());
    String windowKey = "rate_limit:" + key;
    
    // 移除过期记录
    redisTemplate.opsForZSet().removeRangeByScore(
        windowKey, 0, 
        System.currentTimeMillis() - windowSeconds * 1000L);
    
    // 当前窗口请求数
    Long count = redisTemplate.opsForZSet().zCard(windowKey);
    
    if (count != null && count >= limit) {
        return false; // 限流
    }
    
    // 添加请求
    redisTemplate.opsForZSet().add(windowKey, now, 
        Double.parseDouble(now));
    redisTemplate.expire(windowKey, windowSeconds + 1, TimeUnit.SECONDS);
    return true;
}
```

## 降级（Degradation）

### 降级策略

| 策略 | 说明 | 示例 |
|------|------|------|
| **熔断降级** | 触发熔断后返回兜底数据 | 返回缓存数据 |
| **超时降级** | 超时后返回默认值 | 返回空列表 |
| **资源降级** | CPU/内存不足时关闭非核心功能 | 关闭推荐服务 |
| **开关降级** | 通过配置开关手动降级 | 促销时关闭非核心链路 |

### 降级示例

```java
public ProductVO getProductDetail(Long productId) {
    try {
        return productService.getDetail(productId);
    } catch (TimeoutException e) {
        // 超时降级：尝试从缓存获取
        ProductVO cached = cache.get(productId);
        if (cached != null) {
            log.warn("Product {} timeout, fallback to cache", productId);
            return cached;
        }
        // 缓存也没有，返回默认商品
        return ProductVO.defaultProduct();
    }
}
```

## 服务发现

### 注册中心对比

| 组件 | CAP | 一致性 | 特点 |
|------|-----|--------|------|
| **Eureka** | AP | 最终一致 | Spring Cloud 标配，简单 |
| **Nacos** | CP+AP | 可配置 | 阿里开源，功能全面 |
| **ZooKeeper** | CP | 强一致 | 成熟，运维复杂 |
| **Consul** | CP | 强一致 | HashiCorp 开源 |

### 健康检查

| 方式 | 说明 | 优点 | 缺点 |
|------|------|------|------|
| **心跳检测** | 注册中心主动探测 | 实现简单 | 有延迟 |
| **客户端上报** | 客户端主动上报状态 | 实时 | 增加客户端复杂度 |

## 相关

- [Distributed Transaction_分布式事务](Distributed%20Transaction_分布式事务.md)
- [Cache Architecture Design_缓存架构设计](Cache%20Architecture%20Design_缓存架构设计.md)
- [API Gateway_API网关](API%20Gateway_API网关.md)
