---
title: API Gateway（API网关）
type: concept
tags: [microservices, gateway, architecture, api]
sources: []
---

# API Gateway（API网关）

API 网关是微服务架构的统一入口，负责请求路由、协议转换、安全认证、限流熔断等横切关注点，是微服务架构中的核心组件。

## 网关核心功能

```
                    ┌────────────────────────────┐
                    │         API Gateway        │
                    │                            │
┌────────┐         │  ┌────────────────────┐   │
│ Client  │──────────▶ │  认证鉴权         │   │
└────────┘         │  ├────────────────────┤   │
                    │  │  路由分发          │   │
                    │  ├────────────────────┤   │
                    │  │  限流熔断          │   │
                    │  ├────────────────────┤   │
                    │  │  协议转换          │   │
                    │  ├────────────────────┤   │
                    │  │  日志监控          │   │
                    │  └────────────────────┘   │
                    └───────────┬──────────────┘
                                │
        ┌───────────┬───────────┼───────────┬───────────┐
        ▼           ▼           ▼           ▼           ▼
   ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
   │ Order  │ │ User   │ │ Product│ │ Pay    │ │ Search │
   │ Service │ │ Service│ │ Service│ │ Service│ │ Service│
   └────────┘ └────────┘ └────────┘ └────────┘ └────────┘
```

## 路由转发

### 路由规则配置

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: order-service
          uri: lb://order-service  # 负载均衡到注册中心的服务
          predicates:
            - Path=/api/order/**
          filters:
            - StripPrefix=1  # 去掉 /api/order 前缀
          
        - id: user-service
          uri: lb://user-service
          predicates:
            - Path=/api/user/**
          filters:
            - StripPrefix=1
```

### 动态路由

```java
@Bean
public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
    return builder.routes()
        .route("order", r -> r
            .path("/api/order/**")
            .filters(f -> f.stripPrefix(1))
            .uri("lb://order-service"))
        .route("user", r -> r
            .path("/api/user/**")
            .filters(f -> f.stripPrefix(1))
            .uri("lb://user-service"))
        .build();
}
```

## 认证鉴权

### JWT 认证流程

```
1. Client → 网关: 携带 JWT Token
2. 网关验证 Token 签名
3. 验证通过，提取用户信息到请求头
4. 转发到后端微服务
```

### 网关认证配置

```java
@Component
public class JwtAuthenticationFilter implements GlobalFilter {
    
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, 
                             GatewayFilterChain chain) {
        String token = exchange.getRequest().getHeaders()
            .getFirst("Authorization");
        
        if (StringUtils.isBlank(token)) {
            exchange.getResponse().setStatusCode(
                HttpStatus.UNAUTHORIZED);
            return exchange.getResponse().setComplete();
        }
        
        try {
            Claims claims = JwtUtil.parseToken(token);
            // 将用户信息传递给下游服务
            ServerHttpRequest mutated = exchange.getRequest().mutate()
                .header("X-User-Id", claims.getSubject())
                .build();
            return chain.filter(
                exchange.mutate().request(mutated).build());
        } catch (Exception e) {
            exchange.getResponse().setStatusCode(
                HttpStatus.UNAUTHORIZED);
            return exchange.getResponse().setComplete();
        }
    }
}
```

## 限流

### 基于 Redis 的限流

```java
@Component
public class RateLimitFilter implements GatewayFilter {
    
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, 
                             GatewayFilterChain chain) {
        String key = "rate_limit:" + 
            exchange.getRequest().getRemoteAddress();
        int limit = 100;
        int window = 1;
        
        return redisTemplate.execute(new RedisCallback<Boolean>() {
            @Override
            public Boolean doInRedis(RedisConnection connection) 
                    throws DataAccessException {
                long count = connection.incr(key.getBytes());
                if (count == 1) {
                    connection.expire(key.getBytes(), window);
                }
                return count <= limit;
            }
        }).flatMap(allowed -> {
            if (allowed) {
                return chain.filter(exchange);
            }
            exchange.getResponse().setStatusCode(
                HttpStatus.TOO_MANY_REQUESTS);
            return exchange.getResponse().setComplete();
        });
    }
}
```

### 限流策略

| 策略 | 说明 | 配置 |
|------|------|------|
| **单机限流** | 每个网关实例独立限流 | limit=100/s |
| **全局限流** | 所有实例共享限流配额 | Redis 计数 |
| **用户维度** | 按用户 ID 限流 | key=user:{id} |
| **接口维度** | 按路径限流 | key=path:{path} |

## 熔断降级

### Sentinel 网关配置

```java
@Configuration
public class SentinelGatewayConfig {
    
    @PostConstruct
    public void init() {
        GatewayRuleManager.loadRules(List.of(
            FlowRule.of("order-service", 100, 
                ResourceOriginConstants.IN_API),
            FlowRule.of("product-service", 50, 
                ResourceOriginConstants.IN_API)
        ));
    }
}
```

### 网关降级处理

```java
@Configuration
public class GatewayConfig {
    
    @Bean
    @Order(-1)
    public WebExceptionHandler globalExceptionHandler() {
        return (exchange, ex) -> {
            if (ex instanceof BlockException) {
                return writeResponse(exchange.getResponse(),
                    HttpStatus.TOO_MANY_REQUESTS,
                    "请求过于频繁，请稍后重试");
            }
            return Mono.error(ex);
        };
    }
}
```

## 协议转换

### HTTP → RPC

```java
@Component
public class HttpToDubboFilter implements GlobalFilter {
    
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, 
                             GatewayFilterChain chain) {
        // 将 HTTP 请求转换为 Dubbo RPC 调用
        DubboRequest request = convertToDubboRequest(
            exchange.getRequest());
        
        return dubboClient.invoke(request)
            .flatMap(response -> {
                // 将 RPC 响应转换回 HTTP
                return writeJsonResponse(
                    exchange.getResponse(), response);
            });
    }
}
```

## 日志与监控

### 请求日志

```java
@Component
public class AccessLogFilter implements GlobalFilter {
    
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, 
                             GatewayFilterChain chain) {
        long start = System.currentTimeMillis();
        String traceId = UUID.randomUUID().toString();
        
        exchange.getResponse().getHeaders()
            .add("X-Trace-Id", traceId);
        
        return chain.filter(exchange).then(Mono.fromRunnable(() -> {
            long duration = System.currentTimeMillis() - start;
            log.info("traceId={}, path={}, status={}, duration={}ms",
                traceId,
                exchange.getRequest().getPath(),
                exchange.getResponse().getStatusCode(),
                duration);
        }));
    }
}
```

## 主流网关对比

| 网关 | 实现 | 特点 |
|------|------|------|
| **Spring Cloud Gateway** | Java/WebFlux | Spring 生态，无缝集成 |
| **Kong** | Nginx/Lua | 性能高，插件丰富 |
| **Zuul** | Java | Spring Cloud 1.x 使用 |
| **APISIX** | Nginx/Lua | 云原生，动态配置 |

## 相关

- [Service Governance_服务治理](Service%20Governance_服务治理.md)
- [Distributed Lock_分布式锁](Distributed%20Lock_分布式锁.md)
