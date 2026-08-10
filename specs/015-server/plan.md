# 015-server 技术方案 (plan.md)

> 对应规格：spec.md | 模块：015-server | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, Spring Boot Admin 4.1.2
- Jetty Web 容器（排除 Tomcat）
- 独立进程运行在端口 9090，上下文路径 `/admin`

### 1.2 依赖
- `spring-boot-admin-starter-server` — SBA 服务端核心
- `spring-boot-admin-starter-client` — 自注册到自身
- `spring-boot-starter-security` — 表单登录认证
- `spring-boot-starter-web` (Jetty) — Web 容器
- `spring-boot-starter-actuator`（通过 `ruoyi-common-web` 传递）— 暴露端点

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅（轻量） | 独立可部署服务，仅含 config + notifier |
| 注解驱动 | ✅ | @EnableAdminServer、@EnableWebSecurity |
| 组件化 | ✅ | 独立模块 `ruoyi-extend/ruoyi-monitor-admin` |

## 3. 数据模型

### 3.1 核心配置
- `application.yml`（单文件，Maven 过滤的 `@...@` 占位符）：
  - `server.port: 9090`
  - `spring.boot.admin.context-path: /admin`
  - `spring.security.user.name: @monitor.username@` / `password: @monitor.password@`
  - `management.endpoints.web.exposure.include: '*'`
  - `management.endpoint.health.show-details: ALWAYS`
  - `management.endpoint.logfile.external-file: ./logs/ruoyi-monitor-admin.log`（自身日志端点）

### 3.2 关键配置规则
- Maven 占位符 `@monitor.username@` / `@monitor.password@` / `@profiles.active@` 由根 pom.xml profiles 注入
- local/dev 环境凭据：`ruoyi / 123456`
- prod 环境凭据：同 `ruoyi / 123456`（可在 prod profile 中覆盖）

## 4. 接口契约

### 4.1 提供接口
- SBA Server REST API（由 `spring-boot-admin-starter-server` 自动提供）：
  - `GET /admin/applications` — 注册实例列表
  - `GET /admin/applications/{id}` — 实例详情
  - `GET /admin/applications/{id}/metrics` — 度量指标
  - `GET /admin/applications/{id}/logfile` — 在线日志
  - `POST /admin/instances` — 实例注册（供客户端调用）

### 4.2 消费接口
- 各被监控应用的 Actuator 端点（通过 HTTP Basic 认证访问）：
  - `/actuator/health` — 健康检查
  - `/actuator/metrics` — 度量数据
  - `/actuator/logfile` — 日志文件
  - `/actuator/env` — 环境变量与配置

## 5. 实现策略

### 5.1 架构模式
独立服务模式：monitor-admin 是独立的 Spring Boot 应用，与主应用 ruoyi-admin 分离部署。通过 Actuator HTTP 端点拉取各注册实例的监控数据。

### 5.2 关键组件
- **MonitorAdminApplication** — `@EnableAdminServer` + `@SpringBootApplication`，启动监控中心
- **SecurityConfig** — Spring Security 配置：
  - `SecurityFilterChain` 保护 `/admin/**`，放行 `/admin/assets/**` 和 `/admin/login`
  - 表单登录，登录成功后重定向至 `/admin/`
  - CSRF 禁用，X-Frame-Options 禁用（允许嵌入 iframe）
  - HTTP Basic 认证启用（支持客户端注册时的认证）
- **CustomNotifier** — `AbstractEventNotifier` 实现，监听 `InstanceStatusChangedEvent`，中文日志输出状态变更（UP=服务上线、DOWN=服务下线、OFFLINE=服务离线）
- **Client 端 Actuator 安全**（`ruoyi-common-security` 的 `SecurityConfig.java`）：
  - `SaServletFilter` 匹配 `/actuator` 和 `/actuator/**`
  - HTTP Basic 认证，用户名密码来自 `spring.boot.admin.client.username/password`

### 5.3 错误处理
- 实例注册失败 → SBA 内部重试机制
- 监控数据拉取超时 → 显示"离线"状态
- 认证失败 → 返回 401 JSON 响应

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| MonitorAdminApplication | 监控中心主入口 | ruoyi-extend/ruoyi-monitor-admin/.../monitor/admin/ |
| SecurityConfig | 表单登录安全配置 | ruoyi-extend/ruoyi-monitor-admin/.../monitor/admin/config/ |
| CustomNotifier | 状态变更通知器 | ruoyi-extend/ruoyi-monitor-admin/.../monitor/admin/notifier/ |
| application.yml | 单文件多文档配置 | ruoyi-extend/ruoyi-monitor-admin/src/main/resources/ |
| Dockerfile | 容器镜像构建 | ruoyi-extend/ruoyi-monitor-admin/ |
| SecurityConfig (client) | Actuator Basic Auth 过滤 | ruoyi-common-security/.../security/config/ |
| application-dev/prod.yml (client) | 客户端注册配置（enabled=false 默认） | ruoyi-admin/src/main/resources/ |
| nginx.conf | /admin/ 反向代理 | script/docker/nginx/conf/ |
| docker-compose.yml | monitor-admin 服务编排 | script/docker/ |
