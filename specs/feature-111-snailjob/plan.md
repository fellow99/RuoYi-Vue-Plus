# feature-111-snailjob 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-111-snailjob | 最后更新：2026-08-10

## 1. 技术上下文
- SnailJob 2.0.2, JDK 21, Spring Boot 4.1
- 客户端: snail-job-client-starter + snail-job-client-job-core
- 服务端: snail-job-server-starter（独立 Spring Boot 应用）
- 配置: snail-job.enabled=true（客户端条件启用）

## 2. 宪法合规
| 原则 | 状态 |
|------|------|
| 插件化设计 | ✅ 独立模块 ruoyi-common-job（客户端）+ ruoyi-extend/ruoyi-snailjob-server（服务端），按需启用 |
| 分布式就绪 | ✅ SnailJob 原生分布式调度，支持集群部署 |

## 3. 模块结构

### 3.1 客户端（ruoyi-common/ruoyi-common-job）
- config/SnailJobConfig.java — @ConditionalOnProperty(snail-job.enabled=true)，@EnableScheduling + @EnableSnailJob
- 监听 SnailClientStartingEvent 挂载 SnailLogbackAppender 远程日志

### 3.2 服务端（ruoyi-extend/ruoyi-snailjob-server）
- SnailJobServerApplication.java — 启动类，加载 com.aizuda.snailjob.server.SnailJobServerApplication
- application.yml — server.port=8800, context-path=/snail-job
- SecurityConfig.java — 安全过滤器配置
- ActuatorAuthFilter.java — Actuator 端点认证过滤器

## 4. 部署配置
```yaml
# ruoyi-snailjob-server application.yml
server:
  port: 8800
  servlet:
    context-path: /snail-job
spring:
  application:
    name: ruoyi-snailjob-server
  # 数据源配置（独立数据库或与主库共用）
```

## 5. 接口契约
- SnailJob 控制台: http://localhost:8800/snail-job
- SnailJob 服务端内置管理界面，无需额外开发 Controller

## 6. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-job/pom.xml | 依赖 snail-job-client-starter, snail-job-client-job-core, ruoyi-common-core |
| ruoyi-common-job/.../config/SnailJobConfig.java | 客户端配置（@EnableSnailJob + 日志 appender） |
| ruoyi-extend/ruoyi-snailjob-server/pom.xml | 依赖 snail-job-server-starter, scala-library, spring-boot-admin-starter-client |
| ruoyi-extend/ruoyi-snailjob-server/.../SnailJobServerApplication.java | 服务端启动类 |
| ruoyi-extend/ruoyi-snailjob-server/src/main/resources/application.yml | 端口 8800, context-path /snail-job |
| ruoyi-extend/ruoyi-snailjob-server/src/main/resources/application-dev.yml | 开发环境配置 |
| ruoyi-extend/ruoyi-snailjob-server/src/main/resources/application-prod.yml | 生产环境配置 |
| ruoyi-extend/ruoyi-snailjob-server/.../filter/SecurityConfig.java | 安全过滤器配置 |
| ruoyi-extend/ruoyi-snailjob-server/.../filter/ActuatorAuthFilter.java | Actuator 端点认证 |
