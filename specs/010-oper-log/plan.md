# 010-oper-log 技术方案 (plan.md)

> 对应规格：spec.md | 模块：010-oper-log | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17
- Jetty Web 容器（基于 Netty）
- Spring AOP（`@Aspect`）、Spring Event（`ApplicationEventPublisher`）

### 1.2 依赖
- ruoyi-common-log（`@Log` 注解、`LogAspect` 切面、`OperLogEvent`、`BusinessType`/`BusinessStatus` 枚举）
- ruoyi-common-core（`R` 响应、`PageResult`、`ServletUtils`、`AddressUtils` IP 定位）
- ruoyi-common-mybatis（`BaseMapperPlus`、`PageQuery`、`QueryBuilder`）
- ruoyi-common-excel（`ExcelBuilder`、Apache Fesod）
- ruoyi-common-security（Sa-Token `@SaCheckPermission`）
- ruoyi-common-redis（`@Lock4j` 分布式锁）
- ruoyi-common-satoken（`LoginHelper` 获取当前登录用户信息）
- ruoyi-common-json（`JsonUtils` JSON 序列化）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → Service(接口+实现) → Mapper → Entity |
| 注解驱动 | ✅ | `@SaCheckPermission` 权限控制，`@Log` 操作日志记录，`@Lock4j` 分布式锁 |
| 异步处理 | ✅ | `@Async` + `@EventListener` 实现操作日志异步持久化 |
| 事件驱动 | ✅ | `OperLogEvent` → Spring Event → `SysOperLogServiceImpl.recordOper()` |

## 3. 数据模型

### 3.1 核心表
- `sys_oper_log` — 操作日志表（无逻辑删除，物理删除）

### 3.2 关键字段规则
- `oper_id`: 雪花ID主键（`@TableId`）
- `title`: 操作模块名称，来自 `@Log.title()`
- `businessType`: 业务类型枚举序数（0=OTHER, 1=INSERT, 2=UPDATE, 3=DELETE, 4=GRANT, 5=EXPORT, 6=IMPORT, 7=FORCE, 8=GENCODE, 9=CLEAN）
- `status`: 0=正常（成功），1=异常（失败），来自 `BusinessStatus` 枚举
- `operParam`: 请求参数 JSON，截断至 3800 字符
- `jsonResult`: 响应结果 JSON，截断至 3800 字符
- `errorMsg`: 异常消息，截断至 3800 字符
- `costTime`: 接口耗时（毫秒），由 `StopWatch` 计算

## 4. 接口契约

### 4.1 对外 REST API（SysOperlogController，`/monitor/operlog`）

| 方法 | 路径 | 权限 | 说明 |
|------|------|------|------|
| GET | `/list` | `monitor:operlog:list` | 分页查询操作日志 |
| POST | `/export` | `monitor:operlog:export` | 导出操作日志 Excel |
| DELETE | `/{operIds}` | `monitor:operlog:remove` | 批量删除操作日志 |
| DELETE | `/clean` | `monitor:operlog:remove` | 清空操作日志（分布式锁） |

### 4.2 消费接口
- 无跨模块消费，操作日志由内部事件机制驱动

### 4.3 事件契约
- **发布方**: `LogAspect.handleLog()` → `SpringUtils.context().publishEvent(operLog)`
- **消费方**: `SysOperLogServiceImpl.recordOper(OperLogEvent)` — `@Async` + `@EventListener`
- **事件字段**: `OperLogEvent` 与 `SysOperLogBo` 通过 MapStruct 互相转换（`@AutoMapper`）

## 5. 实现策略

### 5.1 架构模式
**事件驱动 + AOP 切面 → 异步持久化**

```
Controller(@Log注解) → LogAspect(@Around) → 构建OperLogEvent → publishEvent()
  → SysOperLogServiceImpl.recordOper(@Async @EventListener) → insertOperlog()
```

### 5.2 关键流程

**操作日志记录流程**:
1. `LogAspect.doAround()` 拦截所有带 `@Log` 注解的方法
2. 执行目标方法前启动 `StopWatch` 计时
3. 方法正常返回 → `handleLog(joinPoint, log, null, jsonResult, stopWatch)` → status=SUCCESS
4. 方法抛出异常 → `handleLog(joinPoint, log, e, null, stopWatch)` → status=FAIL → 重新抛出异常
5. `handleLog()` 从 `LoginHelper.getLoginUser()` 获取当前用户、部门、浏览器、OS 等信息
6. 序列化请求参数（过滤 `MultipartFile`、`HttpServletRequest`、`HttpServletResponse` 等不可序列化类型）
7. 发布 `OperLogEvent` 到 Spring 事件总线
8. `SysOperLogServiceImpl.recordOper()` 异步监听事件，调用 `AddressUtils.getRealAddressByIP()` 解析 IP 归属地，通过 MapStruct 转换为 `SysOperLogBo`，插入数据库

**Excel 导出流程**:
1. `selectOperLogList()` 无条件分页限制（导出全量）
2. `ExcelBuilder.of(list, SysOperLogVo.class)` 使用 Apache Fesod 生成 Excel
3. 通过 `@ExcelDictFormat` 注解自动翻译业务类型（`sys_oper_type`）、状态（`sys_common_status`）等字典字段

### 5.3 错误处理
- `LogAspect.handleLog()` 内部的 `try-catch` 只记录 `log.error`，不向上抛出，确保日志记录失败不影响业务
- Controller 层异常由全局异常处理器统一转为 `R` 响应

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysOperlogController | REST API | system/controller/monitor/ |
| ISysOperLogService | 业务接口 | system/service/ |
| SysOperLogServiceImpl | 业务实现 + 事件监听 | system/service/impl/ |
| SysOperLogMapper | 数据访问 | system/mapper/ |
| SysOperLog.java | 实体（sys_oper_log） | system/domain/ |
| SysOperLogBo.java | 请求体 / 事件转换中间对象 | system/domain/bo/ |
| SysOperLogVo.java | 响应体 / Excel 导出 VO | system/domain/vo/ |
| Log.java | `@Log` 注解定义 | common-log/annotation/ |
| LogAspect.java | AOP 切面实现 | common-log/aspect/ |
| OperLogEvent.java | 操作日志事件 | common-log/event/ |
| BusinessType.java | 业务类型枚举 | common-log/enums/ |
| BusinessStatus.java | 操作状态枚举 | common-log/enums/ |
