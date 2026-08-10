# 定时任务功能规格 (spec.md)

> 模块：013-job | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
定时任务模块基于 SnailJob 分布式任务调度框架，提供任务执行器的注册与实现。SnailJob Server 负责统一的任务调度、分片、重试、DAG 工作流编排，ruoyi-job 模块仅提供 Executor 执行器 Bean 供 SnailJob Server 远程调度。

### 1.2 解决的问题
- 需要分布式任务调度能力，替代传统的 Quartz 单机调度
- 需要支持多种任务类型：普通任务、广播任务、静态分片任务、Map 任务、MapReduce 任务、DAG 工作流任务
- 需要任务执行日志的远程收集与查看（通过 SnailJob Server 控制台）
- 需要统一的调度管理中心，支持任务的分片执行、失败重试、动态启停

### 1.3 范围
- ✅ 提供 SnailJob Executor 执行器 Beans（`@JobExecutor` 注解 + `@Component`）
- ✅ 支持注解式任务（`@JobExecutor` 注解在方法/类上）
- ✅ 支持类式任务（继承 `AbstractJobExecutor`）
- ✅ 通过 `SnailJobConfig` 自动配置 SnailJob 客户端
- ✅ SnailJob Server 作为独立服务（ruoyi-extend/ruoyi-snailjob-server）负责调度管理
- ❌ ruoyi-job 模块不包含任务调度逻辑（调度完全由 SnailJob Server 管理）
- ❌ 不包含任务管理 CRUD API（任务配置在 SnailJob Server 控制台完成，不在 ruoyi-system 中）

## 2. 用户故事
- 作为**运维人员**，我可以在 SnailJob Server 管理后台创建定时任务并指定执行器，以便实现自动化数据处理
- 作为**开发人员**，我可以在 ruoyi-job 模块中编写 `@JobExecutor` Bean，任务会被 SnailJob Server 自动发现并调度
- 作为**运维人员**，我可以在 SnailJob Server 控制台查看任务执行日志、重试记录，以便排查任务失败原因
- 作为**运维人员**，我可以配置 DAG 工作流任务（如微信账单 → 支付宝账单 → 汇总账单），以便实现复杂的数据处理流水线

## 3. 功能需求

- FR-013-001: 系统 MUST 通过 `SnailJobConfig`（`@EnableSnailJob` + `@EnableScheduling`）自动启用 SnailJob 客户端，开关由配置 `snail-job.enabled` 控制
- FR-013-002: 系统 MUST 在 SnailJob 客户端启动时自动挂载远程日志 appender（`SnailLogbackAppender`），使本地日志可推送到 SnailJob Server 控制台查看
- FR-013-003: 系统 MUST 支持注解式 Executor，在 Spring Bean 方法上标注 `@JobExecutor(name = "xxx")` 即可注册为 SnailJob 执行器
- FR-013-004: 系统 MUST 支持类式 Executor，通过继承 `AbstractJobExecutor` 并重写 `doJobExecute()` 方法实现任务逻辑
- FR-013-005: 系统 MUST 支持广播任务（Broadcast Job）：同一任务在所有在线客户端节点上同时执行
- FR-013-006: 系统 MUST 支持静态分片任务（Static Sharding Job）：根据服务端配置的参数范围，在不同节点上并行处理不同数据分片
- FR-013-007: 系统 MUST 支持 Map 任务：根任务将数据拆分为多个分片，分发给不同节点并行执行（只分片不汇总）
- FR-013-008: 系统 MUST 支持 MapReduce 任务：Map 阶段拆分并行执行，Reduce 阶段汇总合并结果
- FR-013-009: 系统 MUST 支持 DAG 工作流任务：通过 `jobArgs.getWfContext()` / `jobArgs.appendContext()` 在工作流节点间传递上下文数据
- FR-013-010: 系统 MUST 使用 `SnailJobLog.REMOTE` 记录任务执行日志到 SnailJob Server，使用 `SnailJobLog.LOCAL` 记录本地日志
- FR-013-011: SnailJob Server MUST 作为独立服务部署在 ruoyi-extend/ruoyi-snailjob-server，提供管理控制台

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| TestAnnoJobExecutor | 注解式示例任务（`@JobExecutor(name = "testJobExecutor")`） | jobExecute(JobArgs) → ExecuteResult |
| TestClassJobExecutor | 类式示例任务（继承 `AbstractJobExecutor`） | doJobExecute(JobArgs) → ExecuteResult |
| TestBroadcastJob | 广播任务示例 | jobExecute(JobArgs)：随机模拟成功/失败 |
| TestStaticShardingJob | 静态分片任务示例 | jobExecute(JobArgs)：按 id 范围分片处理 |
| TestMapJobAnnotation | Map 任务示例（只分片不汇总） | rootMapExecute(MapArgs, MapHandler) + doCalc(MapArgs) |
| TestMapReduceAnnotation1 | MapReduce 任务示例（分片+汇总） | rootMapExecute + doCalc + reduceExecute |
| WechatBillTask / AlipayBillTask / SummaryBillTask | DAG 工作流示例（微信→支付宝→汇总） | jobExecute + WfContext 上下文传递 |
| BillDTO | 账单 DTO（DAG 工作流示例） | billId, type, settlementDate, billAmount |

## 5. 验收场景

### 场景：SnailJob Server 调度执行器
- Given 运维人员在 SnailJob Server 控制台创建了一个定时任务，执行器名称为 `testJobExecutor`
- When 任务触发时间到达
- Then SnailJob Server 将任务分发到 ruoyi-job 模块中的 `TestAnnoJobExecutor.jobExecute()` 方法，执行结果返回给 Server

### 场景：广播任务在所有节点执行
- Given 系统部署了 3 个 ruoyi-job 实例
- When SnailJob Server 触发广播任务 `testBroadcastJob`
- Then 3 个实例同时执行该任务，各自返回执行结果

### 场景：DAG 工作流执行
- Given 运维人员在 SnailJob Server 配置了 DAG 工作流：wechatBillTask → summaryBillTask, alipayBillTask → summaryBillTask
- When 工作流触发
- Then wechatBillTask 和 alipayBillTask 并行执行，各自将账单数据写入 WfContext；summaryBillTask 从上下文读取微信和支付宝账单，汇总总金额

### 场景：任务执行日志查看
- Given 任务 `testJobExecutor` 刚执行完毕
- When 运维人员在 SnailJob Server 控制台查看该任务的执行日志
- Then 可以看到通过 `SnailJobLog.REMOTE.info()` 输出的日志内容

## 6. 非功能需求
- SnailJob 客户端 MUST 通过配置开关 `snail-job.enabled=true` 控制启用/禁用
- 任务执行日志 MUST 使用 `SnailJobLog.REMOTE` 输出到 SnailJob Server，使用 `SnailJobLog.LOCAL` 输出到本地
- 任务执行结果 MUST 返回 `ExecuteResult.success(data)` 或 `ExecuteResult.failure(msg)`
- SnailJob Server 支持多种数据库（MySQL、PostgreSQL 等）作为持久化存储

## 7. 依赖
- SnailJob Client（`com.aizuda.snailjob:snailjob-client-starter`）— 客户端 SDK
- SnailJob Server（`com.aizuda.snailjob:snailjob-server-starter`）— 服务端（ruoyi-extend 独立部署）
- ruoyi-common-job（`SnailJobConfig` 自动配置类）
- ruoyi-common-core（`DateUtils`、`StringUtils` 等工具类）
- ruoyi-common-json（`JsonUtils` JSON 序列化）
- Hutool（`RandomUtil`、`ThreadUtil` 等工具）
