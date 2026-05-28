---
title: Configuration Center（配置中心）
type: concept
tags: [microservices, configuration, architecture, distributed-systems]
sources: []
---

# Configuration Center（配置中心）

配置中心是微服务架构中的基础设施，负责集中管理应用的配置信息，支持配置的动态更新、多环境隔离、版本管理和灰度发布。

## 为什么需要配置中心

| 问题 | 传统方式 | 配置中心 |
|------|---------|---------|
| **配置分散** | 每个服务一堆配置文件 | 集中管理 |
| **修改耗时** | 需要重启服务 | 热更新，无需重启 |
| **环境不一致** | 测试、预发、线上配置不同 | 环境隔离，一键切换 |
| **权限失控** | 谁改的配置不知道 | 操作审计，追责到人 |

## 主流配置中心对比

| 组件 | 公司 | 特点 | 适用场景 |
|------|------|------|---------|
| **Apollo** | 携程 | 功能全面，支持灰度 | 大型企业 |
| **Nacos** | 阿里 | 同时支持注册中心和配置中心 | 中小企业 |
| **Spring Cloud Config** | Spring | 简单，Git 后端 | Spring Cloud 项目 |
| **Disconf** | 百度 | 阿里内部开源 | 灰度发布需求 |

## Apollo 核心概念

### 命名空间（Namespace）

```
应用
 └── Namespace A (application)
 └── Namespace B (datasource)
 └── Namespace C (自定义)
```

| 类型 | 说明 |
|------|------|
| **application** | 应用私有配置 |
| **extension** | 公共配置，可被应用关联 |
| **cluster** | 集群配置，覆盖 namespace |

### 配置维度

| 维度 | 说明 |
|------|------|
| **应用** | 配置属于哪个应用 |
| **环境** | dev/test/uat/prod |
| **集群** | 机房、网络等差异化 |
| **Namespace** | 配置分组 |

## Spring Boot 集成 Apollo

### 引入依赖

```xml
<dependency>
    <groupId>com.ctrip.framework.apollo</groupId>
    <artifactId>apollo-client</artifactId>
    <version>2.1.0</version>
</dependency>
```

### 配置 application.yml

```yaml
apollo:
  bootstrap:
    enabled: true
    namespaces: application
  meta: http://apollo-configservice:8080
  app-id: order-service
  cluster: default
  env: DEV
```

### 获取配置

```java
@Value("${order.timeout:30}")
private int orderTimeout;

@Configuration
public class ApolloConfig {
    
    @Bean
    public Config orderConfig() {
        return ConfigService.getConfig("order-service");
    }
}
```

### 监听配置变更

```java
@PostConstruct
public void init() {
    Config config = ConfigService.getConfig("order-service");
    
    config.addChangeListener(changeEvent -> {
        for (String key : changeEvent.changedKeys()) {
            ChangeType type = changeEvent.getChange(key).getChangeType();
            System.out.println("配置变更: " + key + " = " + 
                changeEvent.getChange(key).getNewValue() + 
                " (类型: " + type + ")");
        }
    });
}
```

## 配置热更新原理

### 原理

```
┌──────────────┐     长轮询      ┌──────────────┐
│              │ ◀─────────────▶ │              │
│   Apollo     │                 │  Config      │
│   Config     │ ◀─────────────▶ │  Server      │
│   Client     │     HTTP         │              │
│              │                  │              │
└──────┬───────┘                  └──────────────┘
       │
       ▼
┌──────────────┐
│   应用内存    │
│  (PropertySource) │
└──────────────┘
```

### @RefreshScope

```java
@RestController
@RefreshScope  // 配置变更后重新创建 Bean
public class ConfigController {
    
    @Value("${feature.enabled:false}")
    private boolean featureEnabled;
    
    @GetMapping("/feature")
    public String feature() {
        return "Feature enabled: " + featureEnabled;
    }
}
```

## 配置隔离策略

### 多环境配置

```
DEV  ──┬── application-dev.yml
TEST ──┼── application-test.yml  
UAT  ──┼── application-uat.yml
PROD ──┴── application-prod.yml
```

### 敏感配置加密

```java
// 使用加密配置
@Configuration
public class SecretConfig {
    
    @ApolloConfig
    private Config config;
    
    public String getPassword() {
        // Apollo 支持加解密配置值
        return config.getProperty("db.password", null);
    }
}
```

### 配置回滚

```java
// 查看配置变更历史
// Apollo Console → 配置管理 → 修改历史
// 支持回滚到指定版本
```

## Nacos 配置管理

### 引入依赖

```xml
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>nacos-config-spring-boot-starter</artifactId>
    <version>0.2.10</version>
</dependency>
```

### 配置 application.yml

```yaml
spring:
  application:
    name: order-service
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
        file-extension: yml
        namespace: dev
        group: DEFAULT_GROUP
```

### 共享配置

```yaml
spring:
  cloud:
    nacos:
      config:
        shared-dataids: common.yml,redis.yml
        refreshable-dataids: common.yml
```

## 配置中心设计要点

| 要点 | 说明 |
|------|------|
| **高可用** | 配置中心本身不能挂，否则所有服务受影响 |
| **配置校验** | 上线前校验配置格式、必填项 |
| **变更通知** | 配置变更后及时推送到所有实例 |
| **版本管理** | 支持配置回滚 |
| **权限控制** | 不同环境、不同应用权限隔离 |
| **审计日志** | 记录谁、什么时候、改了什么配置 |

## 相关

- [Service Governance_服务治理](Service%20Governance_服务治理.md)
- [Spring Framework](Spring%20Framework.md)
