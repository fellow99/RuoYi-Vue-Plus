# SnailJob 分布式任务调度功能规格 (spec.md)

> 模块：feature-111-snailjob | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
集成 SnailJob 2.0.2 分布式任务调度框架，替换传统 Quartz，提供统一调度中心、分片执行、失败重试、DAG 任务流编排等能力，天生支持分布式集群环境。

### 1.2 解决的问题
- Quartz 基于数据库锁，性能差，集群配置复杂
- 缺乏统一的任务调度管理中心和可视化界面
- 需要支持分片广播、失败重试、任务流 DAG 编排等高级特性

### 1.3 范围
- ✅ SnailJob 2.0.2 客户端集成（snail-job-client-starter + snail-job-client-job-core）
- ✅ SnailJob 服务端独立部署（ruoyi-snailjob-server, 端口 8800）
- ✅ 条件启用（snail-job.enabled=true）
- ✅ SnailJob 远程日志 appender 自动挂载
- ✅ Spring Boot Admin 监控集成
- ❌ 具体任务实现（由业务模块提供）

## 2. 用户故事
- 作为**运维人员**，我可以通过 SnailJob 控制台（端口 8800）管理所有定时任务
- 作为**开发人员**，我可以通过 @EnableSnailJob 注解快速集成任务调度客户端
- 作为**管理员**，我可以在控制台查看任务执行日志和历史

## 3. 功能需求
- FR-111-001: 系统 MUST 支持 SnailJob 客户端条件启用
- FR-111-002: 系统 MUST 提供 SnailJob 服务端独立部署（端口 8800）
- FR-111-003: 系统 SHOULD 支持远程日志查看（SnailLogbackAppender）
- FR-111-004: 系统 SHOULD 支持 Spring Boot Admin 监控集成
- FR-111-005: SnailJob 原生支持分片、重试、DAG 任务流（由框架提供，无需额外开发）

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| SnailJobConfig | 客户端配置（@EnableSnailJob + 日志 appender 挂载） |
| SnailJobServerApplication | 服务端启动类（加载 SnailJob Server Starter） |
| SnailLogbackAppender | 远程日志 appender（SnailClientStartingEvent 触发挂载） |

## 5. 部署架构
```
ruoyi-admin (8080) ─── SnailJob Client ───┬── SnailJob Server (8800)
                                          │    ├── 任务管理
                                          │    ├── 调度中心
                                          │    └── 日志查看
ruoyi-job ─────────── SnailJob Client ────┘
  业务任务实现（@SnailJob 注解）
```

## 6. 依赖
- SnailJob 2.0.2（com.aizuda）
  - snail-job-client-starter（客户端自动配置）
  - snail-job-client-job-core（任务执行核心）
  - snail-job-server-starter（服务端独立部署）
- ruoyi-common-core（基础工具类）
- Spring Boot Admin（服务端监控集成）
