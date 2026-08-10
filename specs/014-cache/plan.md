# 014-cache 技术方案 (plan.md)

> 对应规格：spec.md | 模块：014-cache | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, Redisson 3.x（通过 `redisson-spring-boot-starter` 自动配置）
- Spring Data Redis 抽象层（`RedisConnection` / `RedisConnectionFactory`）

### 1.2 依赖
- `redisson-spring-boot-starter` — 自动提供 `RedissonClient`、`RedissonConnectionFactory` Bean
- `redisson-spring-data-27`（自适应版本）— Redisson 对 Spring Data Redis 的适配
- `ruoyi-common-redis` — RedisConfig（编解码器、键前缀、单机/集群模式）、RedisUtils、CacheNames
- `ruoyi-common-security` — Sa-Token 权限控制

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅（轻量） | Controller 直接操作 RedissonConnectionFactory，无需 Service 层 |
| 注解驱动 | ✅ | @SaCheckPermission("monitor:cache:list") 控制访问 |
| 接口契约 | ✅ | 统一 R\<CacheListInfoVo\> 响应格式 |

## 3. 数据模型

### 3.1 核心数据结构
- `CacheListInfoVo` — Java record 内嵌在 CacheController 中，包含：
  - `info: Properties` — Redis INFO 命令的全部属性
  - `dbSize: Long` — 当前数据库的键总数
  - `commandStats: List<Map<String, String>>` — 命令统计，每项含 `name` 和 `calls`

### 3.2 关键字段规则
- `commandStats` 列表解析自 `info("commandstats")` 返回的 `cmdstat_*` 键，去掉 `cmdstat_` 前缀作为命令名
- `info` 包含 Redis 服务器的所有统计段（无过滤），前端可自行选择展示字段
- 无数据库表，纯实时 Redis 数据

## 4. 接口契约

### 4.1 提供接口
- `GET /monitor/cache` — 返回 `R<CacheListInfoVo>`
  - 权限：`monitor:cache:list`
  - 响应示例：`{ "code": 200, "data": { "info": {...}, "dbSize": 1234, "commandStats": [...] } }`

### 4.2 消费接口
- 无跨模块调用，仅供前端监控页面直接消费

## 5. 实现策略

### 5.1 架构模式
单 Controller 模式（无 Service 层）：CacheController 直接注入 `RedissonConnectionFactory`，获取底层 `RedisConnection`，在 finally 块中释放。

### 5.2 关键算法
- **连接获取**：`RedissonConnectionFactory.getConnection()` 获取连接
- **信息获取**：
  - `connection.commands().info("commandstats")` → 解析 `cmdstat_*` 条目为 name/calls 键值对
  - `connection.commands().info()` → 返回全量 Properties（客户端按段解析过滤）
  - `connection.commands().dbSize()` → 返回 Long
- **连接释放**：`RedisConnectionUtils.releaseConnection(connection, connectionFactory)` 在 finally 块中确保归还

### 5.3 错误处理
- Redis 连接异常 → 全局异常处理器转换为 `R.fail()` 响应
- 无数据遮盖 — Redis 返回的实际数据直接透传

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| CacheController | REST API + 内嵌 VO | system/controller/monitor/ |
| RedisConfig | Redisson 客户端配置 | common-redis/config/ |
| RedissonProperties | 配置属性绑定 | common-redis/config/properties/ |
| KeyPrefixHandler | 键前缀处理器 | common-redis/handler/ |
