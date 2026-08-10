# 013-job 技术方案 (plan.md)

> 对应规格：spec.md | 模块：013-job | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1
- SnailJob（开源分布式任务调度框架，替代 Quartz）
- SnailJob Client SDK（`snailjob-client-starter`）集成在 ruoyi-job / ruoyi-common-job 中
- SnailJob Server（独立部署在 ruoyi-extend/ruoyi-snailjob-server）

### 1.2 依赖
- ruoyi-common-job（`SnailJobConfig` 自动配置 `@EnableSnailJob` + `@EnableScheduling`）
- `com.aizuda.snailjob:snailjob-client-starter` — 客户端 SDK
- `com.aizuda.snailjob:snailjob-client-core` — 核心执行器接口（`AbstractJobExecutor`、`JobArgs`、`ExecuteResult`）
- ruoyi-common-json（`JsonUtils` JSON 序列化）
- ruoyi-common-core（工具类：`DateUtils`、`StringUtils`、`StreamUtils`）
- Hutool（`RandomUtil`、`ThreadUtil`、`Convert`）

### 1.3 模块结构
- **ruoyi-modules/ruoyi-job**: 任务执行器 Bean 实现（`@JobExecutor` + `@Component`）
- **ruoyi-common/ruoyi-common-job**: 自动配置（`SnailJobConfig`）
- **ruoyi-extend/ruoyi-snailjob-server**: SnailJob Server 独立服务（Spring Boot 应用，管理控制台）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | ruoyi-job 仅包含 Executor Bean + 实体，无 Controller/Service/Mapper |
| 注解驱动 | ✅ | `@JobExecutor` 注解注册执行器，`@Component` 注册 Spring Bean |
| 自动配置 | ✅ | `SnailJobConfig` 条件装配（`@ConditionalOnProperty`），`@EnableSnailJob` 启用客户端 |
| 任务调度分离 | ✅ | 调度逻辑完全由 SnailJob Server 管理，job 模块只负责执行 |

## 3. 数据模型

### 3.1 无自有数据库表
ruoyi-job 模块不定义任何数据库表。任务配置、执行日志、重试记录等全部由 SnailJob Server 管理（Server 有自己的数据库存储）。

### 3.2 任务参数模型

| 类 | 用途 |
|---|------|
| `JobArgs` | SnailJob 任务执行参数，包含任务参数 `getJobParams()` 和工作流上下文 `getWfContext()` / `appendContext()` |
| `MapArgs` | Map 任务参数，包含分片数据 `getMapResult()` |
| `ReduceArgs` | Reduce 任务参数，包含所有 Map 结果 `getMapResult()` |
| `ExecuteResult` | 任务执行结果：`ExecuteResult.success(data)` 或 `ExecuteResult.failure(msg)` |

### 3.3 实体（Demo 示例）
| 类 | 用途 |
|---|------|
| `BillDTO` | 账单 DTO（DAG 工作流示例，非通用实体） |

## 4. 接口契约

### 4.1 对外 REST API
ruoyi-job 模块**不对外暴露任何 REST API**。所有任务管理和调度通过 SnailJob Server 控制台（Web UI）操作。

### 4.2 SnailJob 执行器注册契约

**注解式注册**（推荐）:
```java
@Component
@JobExecutor(name = "executorName") // 名称即注册ID，Server按名称调度
public class MyExecutor {
    public ExecuteResult jobExecute(JobArgs jobArgs) {
        // 业务逻辑
        return ExecuteResult.success("ok");
    }
}
```

**类式注册**:
```java
@Component
public class MyExecutor extends AbstractJobExecutor {
    @Override
    protected ExecuteResult doJobExecute(JobArgs jobArgs) {
        return ExecuteResult.success("ok");
    }
}
```

**Map/MapReduce 任务**:
- `@MapExecutor` 标注分片方法
- `@ReduceExecutor` 标注汇总方法
- `MapHandler.doMap(partition, "taskName")` 触发分片调度

### 4.3 消费接口
- 无跨模块接口消费

## 5. 实现策略

### 5.1 架构模式
**Client-Executor 模式**（调度与执行分离）

```
SnailJob Server (独立进程)
  ├── 管理控制台 (Web UI)
  ├── 任务调度引擎
  ├── 失败重试引擎
  └── DAG 工作流引擎
       │
       │ HTTP/gRPC 远程调度
       ▼
ruoyi-job 模块 (作为客户端)
  ├── SnailJobConfig (@EnableSnailJob)
  ├── TestAnnoJobExecutor (@JobExecutor)
  ├── TestClassJobExecutor (AbstractJobExecutor)
  ├── TestBroadcastJob, TestStaticShardingJob
  ├── TestMapJobAnnotation (Map)
  ├── TestMapReduceAnnotation1 (MapReduce)
  └── WechatBillTask, AlipayBillTask, SummaryBillTask (DAG)
```

### 5.2 关键流程

**SnailJob 启动流程**:
1. 应用启动 → `SnailJobConfig` 被自动装配（条件 `snail-job.enabled=true`）
2. `@EnableSnailJob` 初始化 SnailJob 客户端 SDK
3. `@EnableScheduling` 启用 Spring 定时任务支持
4. SnailJob 客户端向 Server 注册，上报本节点可用的执行器列表
5. `SnailClientStartingEvent` 触发 → 挂载 `SnailLogbackAppender` 到 Logback Root Logger，实现远程日志推送

**任务执行流程**:
1. SnailJob Server 根据 cron 表达式或手动触发 → 选择目标节点 → 远程调用执行器
2. 执行器 Bean 的 `jobExecute(JobArgs)` 方法被调用
3. 任务中通过 `SnailJobLog.REMOTE.info()` 输出日志 → 日志推送至 Server
4. 返回 `ExecuteResult` → Server 记录执行结果和日志

**DAG 工作流流程**:
1. SnailJob Server 按 DAG 依赖关系调度节点
2. 并行节点（如 wechatBillTask、alipayBillTask）同时执行
3. 通过 `jobArgs.appendContext(key, value)` 写入上下文
4. 下游节点（如 summaryBillTask）通过 `jobArgs.getWfContext(key)` 读取上游结果
5. 所有节点完成后 → DAG 工作流结束

### 5.3 关键算法
- **广播任务**: SnailJob Server 将同一任务分发到所有在线客户端节点，每个节点独立执行
- **静态分片**: 任务参数以逗号分隔（如 `"1,100"` 表示处理 id 1~100），不同分片分配到不同节点并行处理
- **Map/MapReduce**: 根任务拆分数据 → `mapHandler.doMap()` 分发给各节点 → MapReduce 版本还有 Reduce 汇总阶段
- **虚拟线程**: `ThreadUtils.virtualSubmitAll()` 在任务中使用虚拟线程进行并行处理（如 TestMapJobAnnotation）

### 5.4 错误处理
- 任务执行抛出异常 → SnailJob Server 捕获并根据重试策略自动重试
- `ExecuteResult.failure(msg)` → Server 记录失败并可选重试
- 任务日志通过 `SnailJobLog.REMOTE` 输出，异常堆栈也会推送到 Server 管理控制台

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SnailJobConfig.java | 自动配置（`@EnableSnailJob` + Log Appender） | ruoyi-common/ruoyi-common-job/config/ |
| TestAnnoJobExecutor.java | 注解式普通任务示例 | ruoyi-job/snailjob/ |
| TestClassJobExecutor.java | 类式任务示例（继承 AbstractJobExecutor） | ruoyi-job/snailjob/ |
| TestBroadcastJob.java | 广播任务示例 | ruoyi-job/snailjob/ |
| TestStaticShardingJob.java | 静态分片任务示例 | ruoyi-job/snailjob/ |
| TestMapJobAnnotation.java | Map 任务示例（分片不汇总） | ruoyi-job/snailjob/ |
| TestMapReduceAnnotation1.java | MapReduce 任务示例（分片+汇总） | ruoyi-job/snailjob/ |
| WechatBillTask.java | DAG 工作流-微信账单 | ruoyi-job/snailjob/ |
| AlipayBillTask.java | DAG 工作流-支付宝账单 | ruoyi-job/snailjob/ |
| SummaryBillTask.java | DAG 工作流-汇总账单 | ruoyi-job/snailjob/ |
| BillDTO.java | 账单实体 | ruoyi-job/entity/ |
| SnailJobServerApplication.java | SnailJob Server 启动入口 | ruoyi-extend/ruoyi-snailjob-server/ |
| SecurityConfig.java | Server 安全配置（Actuator 认证） | ruoyi-extend/ruoyi-snailjob-server/ |
