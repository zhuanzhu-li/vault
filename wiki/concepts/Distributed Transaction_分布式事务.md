---
title: Distributed Transaction（分布式事务）
type: concept
tags: [distributed-systems, transaction, saga, architecture]
sources: []
---

# Distributed Transaction（分布式事务）

分布式事务是指事务的参与者、事务管理器、存储资源位于分布式系统的不同节点上，需要通过网络通信协调完成的事务。

## 分布式事务的挑战

| 挑战 | 说明 |
|------|------|
| **网络不可靠** | 消息可能丢失、延迟、乱序 |
| **节点可能故障** | 部分成功部分失败 |
| **时钟不一致** | 不同节点时间可能有偏差 |
| **CAP 约束** | 一致性、可用性、分区容错不可兼得 |

## 经典解决方案对比

| 方案 | 协调方式 | 一致性 | 性能 | 复杂度 | 适用场景 |
|------|---------|--------|------|--------|---------|
| **2PC** | 集中式 | 强一致 | 低 | 高 | 同库分库 |
| **3PC** | 集中式 | 强一致 | 中 | 高 | 同库分库 |
| **TCC** | 分布式 | 最终一致 | 高 | 高 | 跨库业务 |
| **Saga** | 分布式 | 最终一致 | 高 | 中 | 长事务 |
| **本地消息表** | 异步 | 最终一致 | 中 | 低 | 低并发 |

## 2PC（两阶段提交）

### 流程

```
┌──────────────────────────────────────────────────────────────┐
│                    Phase 1: Prepare                          │
│  ┌────────┐         ┌─────────┐         ┌─────────┐      │
│  │ TM     │────────▶│  RM 1   │────────▶│  RM 2   │      │
│  │(协调者)│         │(参与者) │         │(参与者) │      │
│  └────────┘         └─────────┘         └─────────┘      │
│       │                  │                    │             │
│       │     预提交请求    │                    │             │
│       │◀─────────────────┼────────────────────│             │
│       │     预提交响应    │                    │             │
│       │         成功/失败   │                    │             │
├──────────────────────────────────────────────────────────────┤
│                    Phase 2: Commit                           │
│       │                  │                    │             │
│       │     commit/rollback                   │             │
│       ▼                  ▼                    ▼             │
│      提交/回滚         提交/回滚           提交/回滚        │
└──────────────────────────────────────────────────────────────┘
```

### Java 实现关键代码

```java
// Coordinator
public void commit() {
    // Phase 1: 预提交
    boolean allPrepared = true;
    for (ResourceManager rm : participants) {
        if (!rm.prepare()) {
            allPrepared = false;
            break;
        }
    }
    
    // Phase 2: 提交或回滚
    if (allPrepared) {
        for (ResourceManager rm : participants) {
            rm.commit();
        }
    } else {
        for (ResourceManager rm : participants) {
            rm.rollback();
        }
    }
}
```

### 问题

| 问题 | 说明 |
|------|------|
| **同步阻塞** | 所有参与者 prepare 阶段阻塞 |
| **单点故障** | 协调者挂了，参与者等待 |
| **数据不一致** | 部分提交后协调者挂了 |

## 3PC（三阶段提交）

### 改进点

```
CanCommit → PreCommit → DoCommit
```

| 阶段 | 改进 |
|------|------|
| **CanCommit** | 协调者询问参与者是否可以提交（不锁定资源） |
| **PreCommit** | 协调者发送预提交，参与者执行但不提交 |
| **DoCommit** | 真正提交或回滚 |

### 解决的问题

- 减少了同步阻塞时间
- 协调者超时后可以自动提交（因为已经 PreCommit）
- 减少了数据不一致风险

## TCC（Try-Confirm-Cancel）

### 核心思想

将业务逻辑拆分为三个独立的接口：

| 阶段 | 说明 | 幂等 | 空回滚 | 悬挂 |
|------|------|------|--------|------|
| **Try** | 预留资源，检查可行性 | ✓ | - | - |
| **Confirm** | 确认执行，使用预留资源 | ✓ | ✓ | ✓ |
| **Cancel** | 取消执行，释放预留资源 | ✓ | ✓ | - |

### 示例：转账

```java
@GlobalTransactional
public void transfer(String from, String to, int amount) {
    // Try: 冻结金额
    accountService.freeze(from, amount);
    accountService.freeze(to, amount);
    
    try {
        // Confirm: 真正扣减
        accountService.debit(from, amount);
        accountService.credit(to, amount);
    } catch (Exception e) {
        // Cancel: 回滚
        accountService.unfreeze(from, amount);
        accountService.unfreeze(to, amount);
        throw e;
    }
}
```

### 问题与处理

| 问题 | 场景 | 解决方案 |
|------|------|---------|
| **空回滚** | Try 未执行，Cancel 执行了 | 记录状态，Cancel 时检查 |
| **悬挂** | Cancel 先执行了，Try 才执行 | 检查状态，拒绝执行 |
| **幂等** | 重复调用 | 幂等键防重 |

## Saga 模式

### 原理

将长事务拆分为多个本地事务，每个本地事务有对应的补偿事务：

```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│ Step 1  │───▶│ Step 2  │───▶│ Step 3  │───▶│ Step 4  │
│ (本地)  │    │ (本地)  │    │ (本地)  │    │ (本地)  │
└─────────┘    └─────────┘    └─────────┘    └─────────┘
     │              │              │              │
     ▼              ▼              ▼              ▼
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│Compensate│◀──│Compensate│◀──│Compensate│◀──│Compensate│
│   3     │    │   2     │    │   1     │    │   0     │
└─────────┘    └─────────┘    └─────────┘    └─────────┘
```

### 与 TCC 对比

| 维度 | TCC | Saga |
|------|-----|------|
| **资源锁定** | Try 阶段锁定资源 | 不锁定，补偿反转 |
| **侵入性** | 高（需实现三个接口） | 低（只需正向和补偿） |
| **性能** | 低（资源被锁定） | 高 |
| **数据一致性** | 强一致 | 最终一致 |
| **适用场景** | 对数据敏感（金钱） | 长流程、低并发 |

## 本地消息表

### 原理

```
┌──────────────────┐      ┌──────────────────┐
│   本地数据库       │      │   消息表         │
│  ┌────────────┐  │      │  ┌────────────┐  │
│  │ 业务表      │  │      │  │ msg_id     │  │
│  │ transaction │──────────▶│  │ status     │  │
│  │   记录      │  │      │  │ msg_content│  │
│  └────────────┘  │      │  └────────────┘  │
└──────────────────┘      └──────────────────┘
                              │
                              ▼
                    ┌──────────────────┐
                    │   定时任务        │
                    │  扫描待发送消息   │
                    │  发送到 MQ       │
                    └──────────────────┘
```

### 流程

1. 业务操作和消息写入同一本地事务
2. 定时任务扫描消息表，发送消息
3. 消费端消费，发送消费成功确认
4. 更新消息状态为已完成

## Seata 框架

| 模式 | 说明 |
|------|------|
| **AT** | 自动补偿，基于 undo log，对代码无侵入 |
| **TCC** | 用户实现三个接口 |
| **Saga** | 状态机模式定义流程 |
| **XA** | 支持 XA 分布式事务 |

## 相关

- [CAP and BASE Theory_CAP与BASE理论](CAP%20and%20BASE%20Theory_CAP与BASE理论.md)
- [Distributed Computing_分布式计算](Distributed%20Computing_分布式计算.md)
- [MySQL Design Guidelines_MySQL设计规约](MySQL%20Design%20Guidelines_MySQL设计规约.md)
