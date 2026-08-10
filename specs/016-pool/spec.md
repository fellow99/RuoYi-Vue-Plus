# 连接池监控功能规格 (spec.md)

> 模块：016-pool | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
连接池监控模块通过 Spring Boot Actuator 和 Micrometer 自动采集 HikariCP 数据库连接池的运行指标，并在 Spring Boot Admin 监控面板中统一展示，帮助运维人员了解数据库连接池的健康状况。

### 1.2 解决的问题
- 运维人员需要了解数据库连接池的活跃连接数、空闲连接数、等待连接数，以便判断连接池是否需要扩容
- 需要在数据库连接出现超时或泄漏时及时发现
- 需要在一个统一的监控面板查看连接池指标，无需登录数据库执行 SQL

### 1.3 范围
- ✅ HikariCP 连接池核心指标自动采集（active / idle / pending / max / min connections）
- ✅ 数据库健康检查（DataSourceHealthIndicator）
- ✅ 通过 Spring Boot Admin 统一面板查看所有服务的连接池状态
- ✅ Actuator `/metrics/hikaricp.*` 端点暴露指标
- ✅ Actuator `/health/db` 端点数据库健康状态
- ❌ 自定义连接池监控控制器（依赖框架自动采集 + SBA 面板展示）
- ❌ 连接池告警阈值（可基于 SBA + 外部监控系统扩展）

## 2. 用户故事
- 作为**运维管理员**，我可以在 Spring Boot Admin 面板查看每个服务的连接池活跃/空闲连接数，以便判断是否需要调整连接池大小
- 作为**DBA**，我可以查看数据库健康状态（UP/DOWN），以便在数据库异常时及时响应
- 作为**开发人员**，我可以通过 `/actuator/metrics/hikaricp.connections.active` 端点直接获取连接池指标，以便集成到自定义监控系统

## 3. 功能需求

- FR-016-001: 系统 MUST 通过 Spring Boot Actuator 自动暴露 HikariCP 连接池指标（Micrometer DataSourcePoolMetrics）
- FR-016-002: 指标 MUST 包含活跃连接数（`hikaricp.connections.active`）、空闲连接数（`idle`）、最大连接数（`max`）、最小连接数（`min`）、等待连接数（`pending`）
- FR-016-003: 指标 MUST 包含连接超时次数（`hikaricp.connections.timeout`）和创建耗时（`creation`）
- FR-016-004: 健康端点 MUST 包含数据库连接状态（`/actuator/health/db`），返回 UP 或 DOWN
- FR-016-005: Actuator 端点 MUST 通过 HTTP Basic 认证保护（与监控中心共享凭据）
- FR-016-006: Nginx MUST 阻止外部直接访问 `/actuator` 路径（仅允许内网或监控中心访问）

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|------|
| HikariDataSource | HikariCP 数据源 | maximumPoolSize, minimumIdle, activeConnections, idleConnections, pendingConnections |
| DataSourcePoolMetrics | Micrometer 自动绑定 | hikaricp.connections.active/idle/pending/max/min/timeout/creation |
| DataSourceHealthIndicator | Spring Boot 健康检查 | 执行 `SELECT 1` 验证连接有效性 |
| DynamicRoutingDataSource | dynamic-datasource 路由数据源 | 管理多个 HikariDataSource 实例（主库 + 从库） |

## 5. 验收场景

### 场景：查看连接池指标
- Given 应用已启动并配置了 HikariCP，注册到 Spring Boot Admin 监控中心
- When 运维管理员登录监控中心 → 打开某应用实例的 "Metrics" 页面
- Then 可看到 `hikaricp.connections.active`、`hikaricp.connections.idle` 等指标实时数据

### 场景：数据库连接失败
- Given 数据库服务不可用或网络中断
- When 监控中心检查该应用的健康端点
- Then `/actuator/health/db` 返回 `{ "status": "DOWN" }`，监控面板显示红色

### 场景：连接池连接等待
- Given 并发请求数超过最大连接池大小
- When 排查连接池状态
- Then `hikaricp.connections.pending` 指标大于 0，`hikaricp.connections.timeout` 可能增加

### 场景：外部访问 Actuator 被拒绝
- Given 用户尝试直接访问 `http://host/actuator/metrics`
- When Nginx 匹配到 `/actuator` 路径规则
- Then 返回 HTTP 403 Forbidden

## 6. 非功能需求
- HikariCP 连接池配置 MUST 通过 `application-{profile}.yml` 集中管理（默认 maxPoolSize=20, minIdle=10）
- 所有服务 MUST 使用 HikariCP 作为数据库连接池（dynamic-datasource 默认集成）
- 指标采集 MUST 零性能开销（Micrometer 异步采集，HikariCP 内置 JMX/MBean）
- 不需要额外的 Micrometer Registry（如 Prometheus），使用 SBA 面板即可查看

## 7. 依赖
- HikariCP（Spring Boot 默认连接池，`spring-boot-starter-jdbc` 自动引入）
- `dynamic-datasource-spring-boot4-starter` (4.5.0) — 多数据源路由
- `spring-boot-starter-actuator` — 暴露 metrics/health 端点（`ruoyi-common-web` 引入）
- Micrometer（`spring-boot-starter-actuator` 传递依赖）— 自动绑定 DataSourcePoolMetrics
- Spring Boot Admin（015-server）— 聚合展示连接池监控数据
