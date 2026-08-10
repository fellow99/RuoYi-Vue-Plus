# 016-pool 技术方案 (plan.md)

> 对应规格：spec.md | 模块：016-pool | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, HikariCP 5.x（Spring Boot 内置默认连接池）
- dynamic-datasource 4.5.0（多数据源路由层）
- Micrometer（actuator 传递依赖，自动绑定 DataSourcePoolMetrics）

### 1.2 依赖
- `spring-boot-starter-jdbc` → HikariCP（自动）
- `dynamic-datasource-spring-boot4-starter` — 多数据源管理，每个数据源独立 HikariCP 池
- `spring-boot-starter-actuator`（`ruoyi-common-web` 引入）— Micrometer + 健康端点
- `spring-boot-admin-starter-client`（015-server 注册）— 监控数据上报

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅（配置驱动） | 无 Controller/Service 代码，纯 YAML 配置 + Actuator 自动采集 |
| 组件化 | ✅ | 通过 `ruoyi-common-web` 统一注入 Actuator，所有 Web 模块自动获得监控能力 |
| 安全优先 | ✅ | Actuator 端点受 HTTP Basic 保护，Nginx 阻止外部直连 |

## 3. 数据模型

### 3.1 核心配置（YAML）
```yaml
spring:
  datasource:
    type: com.zaxxer.hikari.HikariDataSource       # 固定 HikariCP
    dynamic:
      primary: master                              # 主数据源
      hikari:                                      # 全局 HikariCP 配置
        maxPoolSize: 20
        minIdle: 10
        connectionTimeout: 30000
        validationTimeout: 5000
        idleTimeout: 600000
        maxLifetime: 1800000
        keepaliveTime: 30000
      datasource:
        master:
          url: jdbc:mysql://...
          username: root
          password: root
```

### 3.2 Actuator 暴露配置
```yaml
management:
  endpoints:
    web:
      exposure:
        include: '*'                              # 暴露所有端点
  endpoint:
    health:
      show-details: ALWAYS                         # 显示健康详情（含 db 组件）
    logfile:
      external-file: ./logs/sys-console.log        # 在线日志文件路径
```

### 3.3 HikariCP 自动指标（Micrometer）
| 指标名称 | 类型 | 说明 |
|---------|------|------|
| hikaricp.connections.active | Gauge | 当前活跃连接数 |
| hikaricp.connections.idle | Gauge | 当前空闲连接数 |
| hikaricp.connections.max | Gauge | 配置的最大连接数 |
| hikaricp.connections.min | Gauge | 配置的最小空闲连接数 |
| hikaricp.connections.pending | Gauge | 等待获取连接的线程数 |
| hikaricp.connections.timeout | Counter | 连接超时总次数 |
| hikaricp.connections.creation | Timer | 连接创建耗时 |

## 4. 接口契约

### 4.1 提供接口
- `GET /actuator/health/db` — 数据库健康状态（{"status":"UP"} 或 {"status":"DOWN"}）
- `GET /actuator/metrics/hikaricp.connections.active` — 活跃连接数
- `GET /actuator/metrics/hikaricp.connections.idle` — 空闲连接数
- `GET /actuator/metrics` — 所有可用指标列表
  - 认证：HTTP Basic（`spring.boot.admin.client.username/password`）
  - 外部访问：Nginx 返回 403

### 4.2 消费接口
- Spring Boot Admin Server（`GET /admin/applications/{id}/metrics`）自动拉取并展示指标

## 5. 实现策略

### 5.1 架构模式
全自动零代码模式：无自定义 Controller、Service、或 HealthIndicator。连接池监控完全依赖 Spring Boot 自动配置机制：
1. Spring Boot 自动配置 `DataSourcePoolMetrics`（检测到 DataSource 和 MeterRegistry）
2. Spring Boot 自动配置 `DataSourceHealthIndicator`（执行 `SELECT 1` 检测连接）
3. Actuator 端点由 `ruoyi-common-web` 统一暴露（管理端点 + 安全过滤）
4. SBA Client 自动注册并将数据上报监控中心

### 5.2 安全策略
- **内网链路**：SBA Server → HTTP Basic 认证 → 各应用 Actuator
- **外部访问**：Nginx `location ~* /actuator { return 403; }` 直接拦截
- **Actuator 过滤器**：`SaServletFilter` 在 `ruoyi-common-security/SecurityConfig` 中对 `/actuator/**` 实施 Basic Auth

### 5.3 错误处理
- 数据库不可用 → `DataSourceHealthIndicator` 返回 DOWN，`hikaricp.connections.timeout` 递增
- 连接池耗尽 → `hikaricp.connections.pending` > 0，请求线程阻塞等待

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| application-dev.yml | HikariCP + dynamic-datasource 配置 | ruoyi-admin/src/main/resources/ |
| application-prod.yml | 生产环境连接池配置 | ruoyi-admin/src/main/resources/ |
| application.yml | Actuator 管理端点配置 | ruoyi-admin/src/main/resources/ |
| application.yml | （SnailJob/SnailAI）各自的独立 HikariCP | ruoyi-extend/ruoyi-snailjob-server/.../ |
| pom.xml | spring-boot-admin-starter-client | ruoyi-admin/ |
| pom.xml | spring-boot-starter-actuator | ruoyi-common/ruoyi-common-web/ |
| SecurityConfig.java | Actuator Basic Auth 过滤 | ruoyi-common/ruoyi-common-security/.../ |
| nginx.conf | 阻止外部访问 /actuator | script/docker/nginx/conf/ |
