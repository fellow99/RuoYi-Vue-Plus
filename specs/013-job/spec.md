# 013-job 定时任务管理模块规范

## 模块信息

| 属性 | 值 |
|------|-----|
| 模块编号 | 013 |
| 模块名称 | job |
| 中文名称 | 定时任务管理 |
| 所属系统 | RuoYi-Vue-Plus |
| 模块类型 | 任务调度 |
| 技术栈 | SnailJob + Spring Boot |

## 功能概述

定时任务管理模块提供分布式任务调度能力，支持定时任务、分布式任务、Map/MapReduce 任务等多种任务类型，帮助系统自动化执行周期性业务逻辑。

## 功能清单

### 1. 任务类型支持

#### 1.1 Java 接口任务
- 实现特定接口执行任务逻辑
- 支持分片广播
- 支持任务上下文传递

#### 1.2 注解任务
- 使用@Job 注解标记任务
- 支持 Cron 表达式配置
- 自动注册到调度中心

#### 1.3 Map 任务
- 主任务分发数据到多个子任务
- 子任务并行处理
- 适用于大数据量处理

#### 1.4 MapReduce 任务
- Map 阶段：数据分片
- Reduce 阶段：结果汇总
- 支持分布式计算

### 2. 任务调度策略

#### 2.1 定时调度
- 支持 Cron 表达式
- 支持固定频率
- 支持固定延迟

#### 2.2 分片广播
- 广播模式：所有节点执行
- 轮询模式：按顺序分配
- 分片模式：按分片参数分配

#### 2.3 故障转移
- 任务执行失败自动重试
- 节点故障自动转移
- 支持重试次数配置

### 3. 任务管理功能

#### 3.1 任务配置
- 创建、编辑、删除任务
- 启动、停止任务
- 手动触发任务执行

#### 3.2 任务监控
- 任务执行状态监控
- 执行历史记录
- 执行日志查看

#### 3.3 任务统计
- 任务执行成功率
- 任务执行时长统计
- 任务触发次数统计

## 接口规范

### 创建任务
```
POST /job/info
```
**请求参数**:
| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| jobName | String | 是 | 任务名称 |
| jobGroup | String | 是 | 任务分组 |
| cronExpression | String | 是 | Cron 表达式 |
| jobType | String | 是 | 任务类型 |
| jobConfig | String | 否 | 任务配置 JSON |

### 更新任务
```
PUT /job/info
```

### 删除任务
```
DELETE /job/info/{jobId}
```

### 启动/停止任务
```
POST /job/info/{jobId}/start
POST /job/info/{jobId}/stop
```

### 手动触发任务
```
POST /job/info/{jobId}/trigger
```

### 查询任务列表
```
GET /job/info/list
```

### 查询任务执行日志
```
GET /job/log/list?jobId={jobId}
```

## 数据模型

### JobInfo (任务信息)
```java
public class JobInfo {
    /** 任务 ID */
    private Long jobId;
    
    /** 任务名称 */
    private String jobName;
    
    /** 任务分组 */
    private String jobGroup;
    
    /** Cron 表达式 */
    private String cronExpression;
    
    /** 任务类型 */
    private String jobType;
    
    /** 任务配置 */
    private String jobConfig;
    
    /** 状态：0-停止，1-运行 */
    private Integer status;
    
    /** 创建时间 */
    private Date createTime;
}
```

### JobLog (任务执行日志)
```java
public class JobLog {
    /** 日志 ID */
    private Long logId;
    
    /** 任务 ID */
    private Long jobId;
    
    /** 执行时间 */
    private Date executeTime;
    
    /** 执行状态 */
    private String status;
    
    /** 执行时长 */
    private Long duration;
    
    /** 执行结果 */
    private String result;
    
    /** 错误信息 */
    private String errorMsg;
}
```

## 权限配置

| 权限标识 | 权限名称 | 说明 |
|----------|----------|------|
| monitor:job:list | 任务列表 | 查看任务列表 |
| monitor:job:add | 新增任务 | 创建新任务 |
| monitor:job:edit | 编辑任务 | 修改任务配置 |
| monitor:job:delete | 删除任务 | 删除任务 |
| monitor:job:start | 启动任务 | 启动任务执行 |
| monitor:job:stop | 停止任务 | 停止任务执行 |

## 技术实现要点

### 1. SnailJob 集成
- 使用 SnailJob 作为分布式任务调度框架
- 支持多种任务类型和执行模式
- 提供可视化管理界面

### 2. 任务注册
- 自动扫描@Job 注解
- 手动注册任务配置
- 支持动态添加任务

### 3. 任务执行
- 基于线程池执行任务
- 支持任务并发控制
- 支持任务超时控制

### 4. 任务持久化
- 任务配置存储到数据库
- 执行日志存储到数据库
- 支持日志归档

### 5. 分布式协调
- 基于数据库实现分布式锁
- 支持任务分片执行
- 支持故障转移

## 依赖模块

- ruoyi-common-job: 任务调度公共模块
- ruoyi-common-core: 核心工具类
- ruoyi-common-mybatis: 数据库操作
- ruoyi-common-redis: 分布式锁

## 外部依赖

- **SnailJob**: 分布式任务调度框架
- **MySQL**: 任务和日志存储
- **Spring Boot**: 基础框架

## 异常处理

| 异常类型 | 处理方式 |
|----------|----------|
| 任务执行异常 | 记录日志，触发重试 |
| 数据库异常 | 记录日志，告警通知 |
| 网络异常 | 重试机制，故障转移 |

## 日志记录

### 任务执行日志
- 任务 ID、任务名称
- 执行时间、执行时长
- 执行状态、执行结果
- 错误堆栈信息

### 操作日志
- 操作人、操作类型
- 操作时间、操作结果

## 监控指标

- 任务总数、运行中任务数
- 任务执行成功率
- 任务平均执行时长
- 任务失败次数

## 版本历史

| 版本 | 日期 | 变更说明 |
|------|------|----------|
| 1.0 | 2024-01 | 初始版本 |
