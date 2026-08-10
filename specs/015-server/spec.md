# 服务监控功能规格 (spec.md)

> 模块：015-server | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
服务监控模块基于 Spring Boot Admin（SBA）提供集群内所有微服务的实时健康监控面板，包括 CPU 使用率、内存占用、磁盘空间、JVM 堆栈信息、在线日志查看以及 Spring 配置详情。

### 1.2 解决的问题
- 运维人员需要统一监控所有服务的运行状态，无需逐个登录服务器
- 需要在服务出现异常时及时收到通知（上线/离线/宕机状态变更）
- 需要在线查看服务日志，无需 SSH 登录服务器
- 需要查看 JVM 的堆内存、线程、GC 等详细运行指标

### 1.3 范围
- ✅ Spring Boot Admin Server 监控中心（独立服务，端口 9090）
- ✅ 被监控应用自动注册（ruoyi-admin、snailjob-server、snailai-server）
- ✅ CPU、内存、磁盘、JVM、日志实时监控
- ✅ 服务状态变更通知（上线 / 离线 / 宕机）
- ✅ 表单登录认证保护 Admin 面板
- ✅ Nginx 代理暴露 /admin/ 路径访问
- ❌ 自定义采集指标或仪表盘（使用 SBA 内置面板）
- ❌ 邮件/短信告警（可扩展 CustomNotifier）

## 2. 用户故事
- 作为**运维管理员**，我可以打开监控中心面板，查看所有注册服务的健康状态，以便快速定位故障服务
- 作为**运维管理员**，我可以在监控面板查看某服务的 CPU、内存、磁盘使用趋势，以便评估是否需要扩容
- 作为**运维管理员**，我可以在线查看服务的实时日志输出，以便排查问题无需登录服务器
- 作为**开发人员**，我可以查看服务 JVM 的堆内存、GC、线程信息，以便分析性能瓶颈

## 3. 功能需求

- FR-015-001: 系统 MUST 提供独立的 Spring Boot Admin 监控中心服务（`ruoyi-monitor-admin`），端口 9090，上下文路径 `/admin`
- FR-015-002: 系统 MUST 支持 ruoyi-admin、snailjob-server、snailai-server 等应用通过 `spring-boot-admin-starter-client` 自动注册到监控中心
- FR-015-003: 监控中心 MUST 展示每个注册实例的 CPU 使用率、内存使用量、磁盘空间等系统指标
- FR-015-004: 监控中心 MUST 展示每个实例的 JVM 信息（堆内存、非堆内存、线程数、GC 次数与耗时）
- FR-015-005: 监控中心 MUST 支持在线查看注册实例的应用日志（通过 Actuator logfile 端点）
- FR-015-006: 监控中心 MUST 支持查看注册实例的 Spring 配置属性、环境变量和 Beans
- FR-015-007: 监控中心 MUST 在实例状态变更时记录日志通知（上线/离线/宕机/未知）
- FR-015-008: 监控中心面板 MUST 通过表单登录认证保护（Spring Security），用户名密码通过 Maven Profile 注入
- FR-015-009: 系统 MUST 支持 Nginx 反向代理 `/admin/` 路径到监控中心服务
- FR-015-010: 被监控应用 MUST 对 `/actuator/**` 端点实施 HTTP Basic 认证，认证凭据与监控中心保持一致

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| MonitorAdminApplication | 监控中心主应用（@EnableAdminServer） | 端口 9090，context-path /admin |
| Instance | SBA 注册实例 | serviceUrl, status(UP/DOWN/OFFLINE), metadata |
| CustomNotifier | 状态变更通知器 | 监听 InstanceStatusChangedEvent，日志输出 |
| SecurityConfig | 监控面板安全配置 | 表单登录，/admin/assets/** 和 /admin/login 公开 |
| Management Endpoints | Actuator 端点 | health, metrics, logfile, env, configprops, beans 等 |

## 5. 验收场景

### 场景：查看服务监控面板
- Given 运维管理员知道监控中心地址和登录凭据
- When 在浏览器访问 `http://host/admin/` → 输入用户名密码登录
- Then 进入 Spring Boot Admin 面板，看到所有已注册的服务实例及其健康状态

### 场景：服务宕机通知
- Given 某注册应用进程停止或被 kill
- When 监控中心检测到心跳超时
- Then 该实例状态变为 OFFLINE，同时 CustomNotifier 日志输出"服务下线"通知

### 场景：在线查看日志
- Given 运维管理员打开某应用实例详情页
- When 点击"日志"标签页
- Then 可在线查看该应用的 `sys-console.log` 实时日志内容

### 场景：无权限用户访问拒绝
- Given 未登录用户或陌生人
- When 直接访问 `/admin/applications`
- Then 被重定向到 `/admin/login` 表单登录页面

## 6. 非功能需求
- 监控中心 MUST 使用 Jetty 容器（排除 Tomcat），与其他模块保持一致
- 监控数据通过 Actuator 暴露的端点获取（pull 模式），不主动推送
- 监控面板 UI 标题 MUST 显示为 "RuoYi-Vue-Plus服务监控中心"
- Nginx 层 MUST 阻止外部直接访问 `/actuator` 路径（返回 403）

## 7. 依赖
- `spring-boot-admin-starter-server` (4.1.2) — 监控中心服务端
- `spring-boot-admin-starter-client` — 监控中心自注册 + 被监控应用注册
- `spring-boot-starter-security` — 表单登录认证
- `spring-boot-starter-actuator` — 暴露监控端点（`ruoyi-common-web` 引入）
- Sa-Token（`ruoyi-common-security`）— 保护被监控应用的 `/actuator` 端点（HTTP Basic）
- Nginx — 反向代理 `/admin/` 路径
- Docker（Dockerfile + docker-compose）— 容器化部署
