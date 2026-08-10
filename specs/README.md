# 规格文档索引

**项目名称：** RuoYi-Vue-Plus  
**版本：** 6.0.0  
**技术栈：** Spring Boot 4.1 + Sa-Token + MyBatis-Plus + Vue 3 + TypeScript + Element Plus  
**文档生成时间：** 2026-08-10  
**最后更新：** 2026-08-10

---

## 一、文档总览

| 层级 | 分类 | 文档数量 | 说明 |
|------|------|---------|------|
| 整体 | 项目级顶层文档 | 10 | 架构、技术、宪法等全局文档 |
| 整体 | 整体规格文档 | 5 | overall-* 系列文档 |
| 模块 | 系统管理模块 | 8 | 002~009 共 8 个功能模块（001 预留） |
| 模块 | 系统监控模块 | 7 | 010~016 共 7 个功能模块 |
| 模块 | 系统工具模块 | 3 | 017~019 共 3 个功能模块 |
| 模块 | Plus 增强模块 | 13 | feature-101~feature-113 共 13 个功能模块 |
| 模块 | v6 新增模块 | 6 | feature-114~feature-119 共 6 个功能模块 |
| **合计** | **37 目录 / 84 文件** | | |

---

## 二、项目级顶层文档

| 文档 | 路径 | 说明 |
|------|------|------|
| **方案总纲** | [ARCHITECTURE.md](./ARCHITECTURE.md) | 系统整体架构设计：分层架构、模块划分、数据流、部署架构 |
| **技术选型** | [TECH.md](./TECH.md) | 核心技术栈选型理由、版本、依赖说明 |
| **宪法原则** | [constitution.md](./constitution.md) | 项目开发原则、编码规范、治理规则 |
| **项目结构** | [STRUCTURE.md](./STRUCTURE.md) | 源码目录结构、服务清单、配置文件索引 |
| **检查清单** | [SPECS_CHECKLIST.md](./SPECS_CHECKLIST.md) | 规格文档完成度追踪 |

### 整体规格文档

| 文档 | 路径 | 说明 |
|------|------|------|
| **整体规格** | [overall-spec.md](./overall-spec.md) | 系统级功能规格：核心特性、用户故事、非功能需求 |
| **整体方案** | [overall-plan.md](./overall-plan.md) | 系统级技术方案：选型理由、架构决策、关键技术实现 |
| **数据模型** | [overall-data-model.md](./overall-data-model.md) | 全局数据实体定义：核心类型、实体关系 |
| **接口模型** | [overall-api.md](./overall-api.md) | 全局 API 规范：REST 端点清单、认证机制、错误码 |
| **测试用例索引** | [overall-test-cases.md](./overall-test-cases.md) | 全模块功能测试用例索引与通用测试模式 |

---

## 三、系统管理模块（002 ~ 009）

> 编号 001 为 v5 租户管理编号，v6 已移除，预留。

### 002 — 用户管理 (User)

> 系统用户 CRUD、导入导出、角色分配、密码重置、状态管理。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [002-user/spec.md](./002-user/spec.md) | 用户管理功能规格 |
| 技术方案 | [002-user/plan.md](./002-user/plan.md) | 用户管理技术实现方案 |

### 003 — 角色管理 (Role)

> 角色 CRUD、菜单权限分配、数据范围权限、用户分配。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [003-role/spec.md](./003-role/spec.md) | 角色管理功能规格 |
| 技术方案 | [003-role/plan.md](./003-role/plan.md) | 角色管理技术实现方案 |

### 004 — 部门管理 (Dept)

> 组织机构（公司/部门/小组）树形结构，数据权限关联。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [004-dept/spec.md](./004-dept/spec.md) | 部门管理功能规格 |
| 技术方案 | [004-dept/plan.md](./004-dept/plan.md) | 部门管理技术实现方案 |

### 005 — 岗位管理 (Post)

> 用户职务配置，岗位与用户关联。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [005-post/spec.md](./005-post/spec.md) | 岗位管理功能规格 |
| 技术方案 | [005-post/plan.md](./005-post/plan.md) | 岗位管理技术实现方案 |

### 006 — 菜单管理 (Menu)

> 菜单/按钮权限配置、路由管理、权限标识。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [006-menu/spec.md](./006-menu/spec.md) | 菜单管理功能规格 |
| 技术方案 | [006-menu/plan.md](./006-menu/plan.md) | 菜单管理技术实现方案 |

### 007 — 字典管理 (Dict)

> 系统固定数据维护（类型 + 数据），缓存刷新。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [007-dict/spec.md](./007-dict/spec.md) | 字典管理功能规格 |
| 技术方案 | [007-dict/plan.md](./007-dict/plan.md) | 字典管理技术实现方案 |

### 008 — 参数配置 (Config)

> 动态系统参数管理，键值对缓存。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [008-config/spec.md](./008-config/spec.md) | 参数配置功能规格 |
| 技术方案 | [008-config/plan.md](./008-config/plan.md) | 参数配置技术实现方案 |

### 009 — 通知公告 (Notice)

> 通知公告发布、在线用户实时推送。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [009-notice/spec.md](./009-notice/spec.md) | 通知公告功能规格 |
| 技术方案 | [009-notice/plan.md](./009-notice/plan.md) | 通知公告技术实现方案 |

---

## 四、系统监控模块（010 ~ 016）

### 010 — 操作日志 (OperLog)

> 操作日志异步记录与查询、导出、清理。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [010-oper-log/spec.md](./010-oper-log/spec.md) | 操作日志功能规格 |
| 技术方案 | [010-oper-log/plan.md](./010-oper-log/plan.md) | 操作日志技术实现方案 |

### 011 — 登录日志 (LoginLog)

> 登录记录查询、登录异常、账户解锁。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [011-login-log/spec.md](./011-login-log/spec.md) | 登录日志功能规格 |
| 技术方案 | [011-login-log/plan.md](./011-login-log/plan.md) | 登录日志技术实现方案 |

### 012 — 在线用户 (Online)

> 在线用户监控、强制踢出、设备管理。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [012-online/spec.md](./012-online/spec.md) | 在线用户功能规格 |
| 技术方案 | [012-online/plan.md](./012-online/plan.md) | 在线用户技术实现方案 |

### 013 — 定时任务 (Job)

> SnailJob 分布式任务调度管理。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [013-job/spec.md](./013-job/spec.md) | 定时任务功能规格 |
| 技术方案 | [013-job/plan.md](./013-job/plan.md) | 定时任务技术实现方案 |

### 014 — 缓存管理 (Cache)

> Redis 缓存信息查询与命令统计。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [014-cache/spec.md](./014-cache/spec.md) | 缓存管理功能规格 |
| 技术方案 | [014-cache/plan.md](./014-cache/plan.md) | 缓存管理技术实现方案 |

### 015 — 服务监控 (Server)

> Spring Boot Admin 探针，CPU/内存/磁盘/JVM 实时监控。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [015-server/spec.md](./015-server/spec.md) | 服务监控功能规格 |
| 技术方案 | [015-server/plan.md](./015-server/plan.md) | 服务监控技术实现方案 |

### 016 — 连接池监控 (Pool)

> HikariCP 数据库连接池状态监控。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [016-pool/spec.md](./016-pool/spec.md) | 连接池监控功能规格 |
| 技术方案 | [016-pool/plan.md](./016-pool/plan.md) | 连接池监控技术实现方案 |

---

## 五、系统工具模块（017 ~ 019）

### 017 — 代码生成 (Gen)

> 多数据源前后端代码生成，支持 CRUD 下载。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [017-gen/spec.md](./017-gen/spec.md) | 代码生成功能规格 |
| 技术方案 | [017-gen/plan.md](./017-gen/plan.md) | 代码生成技术实现方案 |

### 018 — API 文档 (Swagger)

> SpringDoc 自动生成 API 接口文档（基于 Javadoc）。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [018-swagger/spec.md](./018-swagger/spec.md) | API 文档功能规格 |
| 技术方案 | [018-swagger/plan.md](./018-swagger/plan.md) | API 文档技术实现方案 |

### 019 — 构建工具 (Build)

> Maven 多环境构建、Docker 容器化部署。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [019-build/spec.md](./019-build/spec.md) | 构建工具功能规格 |
| 技术方案 | [019-build/plan.md](./019-build/plan.md) | 构建工具技术实现方案 |

---

## 六、Plus 增强模块（feature-101 ~ feature-113）

### feature-101 — 工作流管理 (Workflow)

> Warm-Flow 工作流引擎，审批、转办、委派、会签等。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-101-workflow/spec.md](./feature-101-workflow/spec.md) | 工作流功能规格 |
| 技术方案 | [feature-101-workflow/plan.md](./feature-101-workflow/plan.md) | 工作流技术实现方案 |

### feature-102 — 示例模块 (Demo)

> 框架功能使用示例（缓存/锁/限流/短信/邮件/加密/脱敏/MQTT/MCP/ES 等）。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-102-demo/spec.md](./feature-102-demo/spec.md) | 示例模块功能规格 |
| 技术方案 | [feature-102-demo/plan.md](./feature-102-demo/plan.md) | 示例模块技术实现方案 |

### feature-103 — 对象存储 (OSS)

> MinIO / RustFS / S3 文件管理，多配置切换。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-103-oss/spec.md](./feature-103-oss/spec.md) | 对象存储功能规格 |
| 技术方案 | [feature-103-oss/plan.md](./feature-103-oss/plan.md) | 对象存储技术实现方案 |

### feature-104 — 加密模块 (Encrypt)

> BASE64 / AES / RSA / SM2 / SM4 数据库字段和 API 传输加解密。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-104-encrypt/spec.md](./feature-104-encrypt/spec.md) | 加密模块功能规格 |
| 技术方案 | [feature-104-encrypt/plan.md](./feature-104-encrypt/plan.md) | 加密模块技术实现方案 |

### feature-105 — 数据脱敏 (Sensitive)

> 身份证/手机号/地址/邮箱/银行卡等注解式脱敏。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-105-sensitive/spec.md](./feature-105-sensitive/spec.md) | 数据脱敏功能规格 |
| 技术方案 | [feature-105-sensitive/plan.md](./feature-105-sensitive/plan.md) | 数据脱敏技术实现方案 |

### feature-106 — 幂等控制 (Idempotent)

> 分布式幂等，防止重复提交。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-106-idempotent/spec.md](./feature-106-idempotent/spec.md) | 幂等控制功能规格 |
| 技术方案 | [feature-106-idempotent/plan.md](./feature-106-idempotent/plan.md) | 幂等控制技术实现方案 |

### feature-107 — 限流控制 (Ratelimiter)

> Redis 分布式限流，IP / 集群 / 自定义维度。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-107-ratelimiter/spec.md](./feature-107-ratelimiter/spec.md) | 限流控制功能规格 |
| 技术方案 | [feature-107-ratelimiter/plan.md](./feature-107-ratelimiter/plan.md) | 限流控制技术实现方案 |

### feature-108 — 短信服务 (SMS)

> SMS4J 多厂家短信发送。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-108-sms/spec.md](./feature-108-sms/spec.md) | 短信服务功能规格 |
| 技术方案 | [feature-108-sms/plan.md](./feature-108-sms/plan.md) | 短信服务技术实现方案 |

### feature-109 — 社交登录 (Social)

> JustAuth 第三方登录（微信/钉钉/码云等）。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-109-social/spec.md](./feature-109-social/spec.md) | 社交登录功能规格 |
| 技术方案 | [feature-109-social/plan.md](./feature-109-social/plan.md) | 社交登录技术实现方案 |

### feature-110 — 邮件服务 (Mail)

> 通用邮件协议发送（文本/附件/HTML）。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-110-mail/spec.md](./feature-110-mail/spec.md) | 邮件服务功能规格 |
| 技术方案 | [feature-110-mail/plan.md](./feature-110-mail/plan.md) | 邮件服务技术实现方案 |

### feature-111 — 分布式任务 (SnailJob)

> SnailJob 统一调度中心（分片/重试/DAG 任务流）。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-111-snailjob/spec.md](./feature-111-snailjob/spec.md) | 分布式任务功能规格 |
| 技术方案 | [feature-111-snailjob/plan.md](./feature-111-snailjob/plan.md) | 分布式任务技术实现方案 |

### feature-112 — WebSocket

> Token 鉴权 + 分布式会话同步的 WebSocket 实时通信。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-112-websocket/spec.md](./feature-112-websocket/spec.md) | WebSocket 功能规格 |
| 技术方案 | [feature-112-websocket/plan.md](./feature-112-websocket/plan.md) | WebSocket 技术实现方案 |

### feature-113 — SSE 推送

> Token 鉴权的服务端事件推送。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-113-sse/spec.md](./feature-113-sse/spec.md) | SSE 推送功能规格 |
| 技术方案 | [feature-113-sse/plan.md](./feature-113-sse/plan.md) | SSE 推送技术实现方案 |

---

## 七、v6 新增模块（feature-114 ~ feature-119）

### feature-114 — AI 模块 (AI)

> Spring AI 2.0 + SnailAI，多模型统一接口，MCP 协议。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-114-ai/spec.md](./feature-114-ai/spec.md) | AI 模块功能规格 |
| 技术方案 | [feature-114-ai/plan.md](./feature-114-ai/plan.md) | AI 模块技术实现方案 |

### feature-115 — LiteFlow 规则引擎

> 组件化业务编排，EL 表达式定义规则链。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-115-liteflow/spec.md](./feature-115-liteflow/spec.md) | LiteFlow 功能规格 |
| 技术方案 | [feature-115-liteflow/plan.md](./feature-115-liteflow/plan.md) | LiteFlow 技术实现方案 |

### feature-116 — MQTT 协议

> Mica-MQTT 物联网消息协议客户端。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-116-mqtt/spec.md](./feature-116-mqtt/spec.md) | MQTT 功能规格 |
| 技术方案 | [feature-116-mqtt/plan.md](./feature-116-mqtt/plan.md) | MQTT 技术实现方案 |

### feature-117 — MCP 协议

> Model Context Protocol Server，AI 工具调用标准化。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-117-mcp/spec.md](./feature-117-mcp/spec.md) | MCP 协议功能规格 |
| 技术方案 | [feature-117-mcp/plan.md](./feature-117-mcp/plan.md) | MCP 协议技术实现方案 |

### feature-118 — 消息推送 (Push)

> 统一 SSE / WebSocket 消息推送，消息盒子。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-118-push/spec.md](./feature-118-push/spec.md) | 消息推送功能规格 |
| 技术方案 | [feature-118-push/plan.md](./feature-118-push/plan.md) | 消息推送技术实现方案 |

### feature-119 — Elasticsearch

> Easy-Es 全文搜索引擎集成。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-119-elasticsearch/spec.md](./feature-119-elasticsearch/spec.md) | Elasticsearch 功能规格 |
| 技术方案 | [feature-119-elasticsearch/plan.md](./feature-119-elasticsearch/plan.md) | Elasticsearch 技术实现方案 |

---

## 八、模块编号一览

| 编号 | 模块名 | 英文名 | 分类 | 备注 |
|------|--------|--------|------|------|
| 001 | ～预留～ | — | — | v5 租户管理已移除 |
| 002 | 用户管理 | User | 系统管理 | |
| 003 | 角色管理 | Role | 系统管理 | |
| 004 | 部门管理 | Dept | 系统管理 | |
| 005 | 岗位管理 | Post | 系统管理 | |
| 006 | 菜单管理 | Menu | 系统管理 | |
| 007 | 字典管理 | Dict | 系统管理 | |
| 008 | 参数配置 | Config | 系统管理 | |
| 009 | 通知公告 | Notice | 系统管理 | |
| 010 | 操作日志 | OperLog | 系统监控 | |
| 011 | 登录日志 | LoginLog | 系统监控 | |
| 012 | 在线用户 | Online | 系统监控 | |
| 013 | 定时任务 | Job | 系统监控 | |
| 014 | 缓存管理 | Cache | 系统监控 | |
| 015 | 服务监控 | Server | 系统监控 | |
| 016 | 连接池监控 | Pool | 系统监控 | |
| 017 | 代码生成 | Gen | 系统工具 | |
| 018 | API 文档 | Swagger | 系统工具 | |
| 019 | 构建工具 | Build | 系统工具 | |
| 101 | 工作流管理 | Workflow | Plus 增强 | |
| 102 | 示例模块 | Demo | Plus 增强 | |
| 103 | 对象存储 | OSS | Plus 增强 | |
| 104 | 加密模块 | Encrypt | Plus 增强 | |
| 105 | 数据脱敏 | Sensitive | Plus 增强 | |
| 106 | 幂等控制 | Idempotent | Plus 增强 | |
| 107 | 限流控制 | Ratelimiter | Plus 增强 | |
| 108 | 短信服务 | SMS | Plus 增强 | |
| 109 | 社交登录 | Social | Plus 增强 | |
| 110 | 邮件服务 | Mail | Plus 增强 | |
| 111 | 分布式任务 | SnailJob | Plus 增强 | |
| 112 | WebSocket | WebSocket | Plus 增强 | |
| 113 | SSE 推送 | SSE | Plus 增强 | |
| 114 | AI 模块 | AI | v6 新增 | |
| 115 | LiteFlow 规则引擎 | LiteFlow | v6 新增 | |
| 116 | MQTT 协议 | MQTT | v6 新增 | |
| 117 | MCP 协议 | MCP | v6 新增 | |
| 118 | 消息推送 | Push | v6 新增 | |
| 119 | Elasticsearch | Elasticsearch | v6 新增 | |

---

## 九、模块文档结构规范

每个模块目录 `NNN-name/` 或 `feature-NNN-name/` 下包含以下 2 份标准文档：

| 文件 | 命名 | 说明 |
|------|------|------|
| 功能规格 | `spec.md` | 定义模块的功能需求、用户故事、验收标准（技术无关） |
| 技术方案 | `plan.md` | 模块的技术实现方案、架构决策、接口契约、文件清单 |

---

## 十、快速导航

| 目标读者 | 推荐阅读顺序 |
|---------|-------------|
| **新加入开发者** | constitution.md → STRUCTURE.md → overall-spec.md → 具体模块 spec.md |
| **架构师 / Tech Lead** | ARCHITECTURE.md → TECH.md → overall-plan.md → overall-api.md |
| **后端开发** | overall-api.md → overall-data-model.md → 对应模块的 plan.md |
| **前端开发** | STRUCTURE.md → overall-api.md → 对应模块的 spec.md |
| **测试 / QA** | overall-test-cases.md → overall-spec.md → 各模块 spec.md |
| **产品经理** | overall-spec.md → 对应模块 spec.md |

---

**文档维护者：** RuoYi-Vue-Plus 开发团队  
**文档仓库：** `RuoYi-Vue-Plus/specs/`  
**最后更新：** 2026-08-10
