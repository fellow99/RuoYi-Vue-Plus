# 整体架构文档 (ARCHITECTURE.md)

**版本：** 6.0.0  
**最后更新：** 2026-08-10  
**项目：** RuoYi-Vue-Plus

---

## 一、系统架构概述

### 1.1 架构风格

RuoYi-Vue-Plus v6.0.0 采用**前后端分离的分层架构 + 插件化模块设计**（相比 v5 移除多租户）：

```
┌─────────────────────────────────────────────────────────────┐
│                      用户浏览器                              │
│                (Vue3 + TS + Element Plus)                    │
└─────────────────────────────────────────────────────────────┘
                              │
                              │ HTTP/HTTPS + JSON
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      Nginx 网关                              │
│                  (反向代理/负载均衡)                          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      后端服务层                              │
│         (Spring Boot 4.1 + Sa-Token + JWT + Jetty)          │
│  ┌─────────────┬─────────────┬─────────────┬─────────────┐  │
│  │  Controller │   Service   │   Mapper    │   Entity    │  │
│  │    层       │    层       │    层       │    层       │  │
│  └─────────────┴─────────────┴─────────────┴─────────────┘  │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
         ┌─────────┐    ┌─────────┐    ┌─────────┐
         │  MySQL  │    │  Redis  │    │ MinIO / │
         │ 数据库   │    │  缓存   │    │ RustFS  │
         └─────────┘    └─────────┘    └─────────┘
```

### 1.2 架构特点

- **前后端分离：** 前端独立项目（plus-ui），后端纯 API 服务
- **RESTful API：** 标准化接口设计，统一 JSON 响应格式
- **无状态认证：** JWT Token 认证，支持水平扩展
- **插件化模块：** 多模块 Maven 项目，ruoyi-common 25 个子模块按需引入
- **AOP 切面：** 统一处理日志、权限、事务、幂等、限流

### 1.3 v6 vs v5 架构变化

| 变化项 | v5 | v6 |
|--------|-----|-----|
| 多租户 | ✅ ruoyi-common-tenant | ❌ 移除 |
| Web 容器 | Undertow | Jetty 12 (Netty) |
| Spring Boot | 3.5 | 4.1 |
| AI 集成 | ❌ | ✅ Spring AI 2.0 + SnailAI |
| Elasticsearch | ❌ | ✅ Easy-Es |
| MCP 协议 | ❌ | ✅ |
| MQTT 协议 | ❌ | ✅ |
| LiteFlow | ❌ | ✅ |
| 消息推送 | 分散在 SSE/WebSocket | ✅ 统一 common-push |

---

## 二、系统分层架构

### 2.1 四层架构模型

```
┌────────────────────────────────────────────────────────────┐
│                    Controller 层                            │
│  • 接收 HTTP 请求 /rest/api 前缀                             │
│  • 参数校验（Validation 注解）                               │
│  • 调用 Service 层 / RPC API                                │
│  • 返回统一响应格式 R<V>                                    │
└────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────┐
│                     Service 层                              │
│  • 业务逻辑实现（接口 + 实现分离）                             │
│  • 事务控制（@Transactional）                                │
│  • 调用 Mapper 层 + 跨模块 RPC                              │
│  • 数据组装（MapStruct-Plus）                                │
└────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────┐
│                     Mapper 层                               │
│  • 数据库访问（BaseMapperPlus + MPJBaseMapper）              │
│  • 数据权限过滤（PlusDataPermissionInterceptor）             │
│  • SQL 日志（SqlLogInterceptor）                             │
│  • 雪花 ID 生成                                             │
└────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────┐
│                     Entity 层                               │
│  • 数据实体定义（@TableName 映射）                            │
│  • 继承 BaseEntity（通用审计字段）                            │
│  • BO（请求体）、VO（响应体）                                 │
└────────────────────────────────────────────────────────────┘
```

### 2.2 跨模块通信

系统通过 `ruoyi-api` 模块实现跨模块服务调用：各业务模块将需要暴露的接口定义在 `ruoyi-api` 中，由 `ruoyi-system` 实现，其他模块（如 `ruoyi-workflow`、`ruoyi-job`）通过 Spring 依赖注入调用。

```
ruoyi-api (接口定义)
    ↓ 实现
ruoyi-system → ISysUserService, ISysDeptService, IConfigService ...
    ↓ 消费
ruoyi-workflow, ruoyi-gen, ruoyi-job, ruoyi-demo
```

---

## 三、模块依赖关系

### 3.1 模块结构

```
RuoYi-Vue-Plus/
├── ruoyi-admin/           # 主启动模块（Spring Boot 入口）
├── ruoyi-api/             # 跨模块 RPC 接口层
├── ruoyi-common/          # 通用基础设施层（25 个子模块）
├── ruoyi-extend/          # 独立部署扩展服务（3 个）
├── ruoyi-modules/         # 业务模块层（6 个）
└── script/                # 部署与数据库脚本
```

### 3.2 依赖关系图

```
                    ┌─────────────────┐
                    │   ruoyi-admin   │  ← Spring Boot 启动入口
                    └────────┬────────┘
                             │ 依赖所有模块
          ┌──────────────────┼──────────────────┐
          ▼                  ▼                  ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│  ruoyi-modules  │ │  ruoyi-extend   │ │    ruoyi-api    │
│  • ruoyi-system │ │  • monitor-admin│ │  (接口定义)      │
│  • ruoyi-gen    │ │  • snailjob-srv │ └─────────────────┘
│  • ruoyi-job    │ │  • snailai-srv  │
│  • ruoyi-demo   │ └─────────────────┘
│  • ruoyi-workflow│
│  • ruoyi-ai     │
└────────┬────────┘
         │ 全部依赖
         ▼
┌─────────────────────────────────────────────────────────┐
│                     ruoyi-common                        │
│  core | web | security | satoken | mybatis | redis      │
│  doc | json | log | encrypt | sensitive | translation   │
│  excel | job | oss | sms | social | mail | push         │
│  ai | liteflow | mqtt | mcp | elasticsearch | bom       │
└─────────────────────────────────────────────────────────┘
```

---

## 四、部署架构

### 4.1 服务拓扑

```
                        Internet
                           │
                           ▼
                   ┌───────────────┐
                   │  Nginx :80    │  反向代理 + 静态资源
                   └───────┬───────┘
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ ruoyi-admin:8080│ │ ruoyi-admin:8081│ │ monitor:9090    │
│ (主应用实例 1)   │ │ (主应用实例 2)   │ │ (SBA 监控)      │
└────────┬────────┘ └────────┬────────┘ └─────────────────┘
         │                   │
         └───────────────────┼───────────────────┐
                             ▼                   ▼
                    ┌───────────────┐   ┌───────────────┐
                    │ MySQL :3306   │   │ Redis :6379   │
                    └───────────────┘   └───────────────┘
                             │
         ┌───────────────────┼───────────────────┐
         ▼                   ▼                   ▼
┌───────────────┐   ┌───────────────┐   ┌───────────────┐
│ snailjob:8800 │   │ snailai:8900  │   │  MinIO :9000  │
│ (任务调度)     │   │ (AI 服务)     │   │  (文件存储)    │
└───────────────┘   └───────────────┘   └───────────────┘
```

### 4.2 Docker 部署

完整 Docker Compose 编排（`script/docker/docker-compose.yml`）：
- **MySQL 8.4.9** — 主数据库
- **Redis 8.6.3** — 缓存 / 分布式锁
- **Nginx 1.31.1** — 反向代理
- **MinIO (pgsty)** — 分布式文件存储
- **ruoyi-server × 2** — 主应用集群（端口 8080/8081）
- **ruoyi-monitor-admin** — Spring Boot Admin（端口 9090）
- **ruoyi-snailjob-server** — SnailJob 调度中心（端口 8800）
- **ruoyi-snailai-server** — SnailAI 服务（端口 8900）

### 4.3 各服务端口

| 服务 | 端口 | 协议 | 说明 |
|------|------|------|------|
| Nginx | 80 | HTTP | 反向代理入口 |
| ruoyi-admin | 8080 | HTTP | 主应用 REST API |
| ruoyi-monitor-admin | 9090 | HTTP | SBA 监控面板 |
| ruoyi-snailjob-server | 8800 | HTTP | SnailJob 管理面板 |
| ruoyi-snailai-server | 8900 | HTTP | SnailAI 管理面板 |
| MySQL | 3306 | TCP | 数据库 |
| Redis | 6379 | TCP | 缓存 |
| MinIO | 9000 | HTTP | 对象存储 |

---

## 五、数据流

### 5.1 请求处理流程

```
1. 用户请求 → Nginx 反向代理 → ruoyi-admin
2. Filter 链：XSS 过滤 → 重复读取 → API 解密
3. Sa-Token 拦截器：JWT 解析 → 登录校验 → 权限校验
4. Controller：参数校验 → 调用 Service
5. Service：业务逻辑 → Mapper 调用（数据权限自动注入）
6. Mapper：MyBatis-Plus 执行 SQL → HikariCP → MySQL
7. Response：Jackson 序列化（脱敏/翻译）→ JSON → 客户端
```

### 5.2 实时通信流

```
消息推送：Service → PushHelper → SSE/WebSocket → 客户端浏览器
工作流通知：WarmFlow 事件 → MessageService → SSE → 用户浏览器
操作日志：AOP 拦截 → OperLogEvent → 异步存储 → MySQL
登录日志：AuthController → LoginInfoEvent → 异步存储 → MySQL
```

---

## 六、关键架构决策

### 6.1 Jetty 替代 Undertow
Spring Boot 4.x 将 Jetty 作为推荐容器，基于 Netty 底层提供更好的异步性能和虚拟线程兼容。

### 6.2 移除多租户
v6 聚焦分布式集群场景，移除 v5 的多租户（ruoyi-common-tenant），简化架构，降低入门门槛。

### 6.3 统一消息推送
v6 新增 `ruoyi-common-push`，统一 SSE 和 WebSocket 消息推送通道，默认使用 SSE（轻量级服务端推送）。

### 6.4 API 接口层
新增 `ruoyi-api` 模块作为跨模块 RPC 接口定义层，各模块通过接口编程解耦。

### 6.5 AI 集成
引入 Spring AI 2.0 + SnailAI，提供标准化的 AI 模型调用接口和 MCP 协议支持。
