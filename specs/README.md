# 规格文档索引

**项目名称：** RuoYi-Vue-Plus  
**版本：** 5.6.0  
**技术栈：** Spring Boot 3.5 + Sa-Token + MyBatis-Plus + Vue 3 + TypeScript + Element Plus  
**文档生成时间：** 2026-05-05  
**最后更新：** 2026-05-05

---

## 一、文档总览

| 层级 | 分类 | 文档数量 | 说明 |
|------|------|---------|------|
| 整体 | 项目级顶层文档 | 10 | 架构、技术、宪法等全局文档 |
| 整体 | 整体规格文档 | 4 | overall-* 系列文档 |
| 模块 | 系统管理模块 | 11 | 001~011 共 11 个功能模块 |
| 模块 | 系统监控模块 | 5 | 012~016 共 5 个功能模块 |
| 模块 | 系统工具模块 | 3 | 017~019 共 3 个功能模块 |
| 模块 | Plus 增强模块 | 13 | feature-101~feature-113 共 13 个功能模块 |
| **合计** | **42 目录 / 210+ 文件** | | |

---

## 二、项目级顶层文档

全局性的架构、技术、宪法等文档，定义项目基线和开发准则。

| 文档 | 路径 | 说明 |
|------|------|------|
| **方案总纲** | [ARCHITECTURE.md](./ARCHITECTURE.md) | 系统整体架构设计：分层架构、模块划分、数据流、部署架构 |
| **技术选型** | [TECH.md](./TECH.md) | 核心技术栈选型理由、版本、依赖说明 |
| **宪法原则** | [constitution.md](./constitution.md) | 项目开发原则、编码规范、治理规则 |
| **项目结构** | [STRUCTURE.md](./STRUCTURE.md) | 源码目录结构、路由清单、组件清单、API 清单 |
| **检查清单** | [SPECS_CHECKLIST.md](./SPECS_CHECKLIST.md) | 规格文档完成度追踪，210+ 份文档的完成状态 |

### 整体规格文档

描述跨模块的全局规格、方案和数据模型。

| 文档 | 路径 | 说明 |
|------|------|------|
| **整体规格** | [overall-spec.md](./overall-spec.md) | 系统级功能规格：核心特性、用户故事、非功能需求 |
| **整体方案** | [overall-plan.md](./overall-plan.md) | 系统级技术方案：选型理由、架构决策、关键技术实现 |
| **数据模型** | [overall-data-model.md](./overall-data-model.md) | 全局数据实体定义：核心类型、枚举、实体关系 |
| **接口模型** | [overall-api.md](./overall-api.md) | 全局 API 规范：请求/响应格式、认证机制、错误码 |

---

## 三、系统管理模块（001 ~ 011）

### 001 — 租户管理 (Tenant)

> 系统内租户的管理：租户套餐、过期时间、用户数量、企业信息等。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [001-tenant/spec.md](./001-tenant/spec.md) | 租户管理功能规格 |
| API 文档 | [001-tenant/api.md](./001-tenant/api.md) | 租户管理 API 接口定义 |
| 数据模型 | [001-tenant/data-model.md](./001-tenant/data-model.md) | 租户实体与类型定义 |
| 页面清单 | [001-tenant/pages.md](./001-tenant/pages.md) | 租户管理页面路由与组件清单 |
| 用户故事 | [001-tenant/user-stories.md](./001-tenant/user-stories.md) | 租户管理详细用户故事 |

---

### 002 — 用户管理 (User)

> 用户的管理配置：新增用户、分配用户所属部门、角色、岗位等。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [002-user/spec.md](./002-user/spec.md) | 用户管理功能规格 |
| API 文档 | [002-user/api.md](./002-user/api.md) | 用户管理 API 接口定义 |
| 数据模型 | [002-user/data-model.md](./002-user/data-model.md) | 用户实体与类型定义 |
| 页面清单 | [002-user/pages.md](./002-user/pages.md) | 用户管理页面路由与组件清单 |
| 用户故事 | [002-user/user-stories.md](./002-user/user-stories.md) | 用户管理详细用户故事 |

---

### 003 — 角色管理 (Role)

> 角色的管理配置：新增角色、分配菜单权限、数据权限等。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [003-role/spec.md](./003-role/spec.md) | 角色管理功能规格 |
| API 文档 | [003-role/api.md](./003-role/api.md) | 角色管理 API 接口定义 |
| 数据模型 | [003-role/data-model.md](./003-role/data-model.md) | 角色实体与类型定义 |
| 页面清单 | [003-role/pages.md](./003-role/pages.md) | 角色管理页面路由与组件清单 |
| 用户故事 | [003-role/user-stories.md](./003-role/user-stories.md) | 角色管理详细用户故事 |

---

### 004 — 部门管理 (Dept)

> 部门的组织架构管理：公司、部门、小组树形结构配置。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [004-dept/spec.md](./004-dept/spec.md) | 部门管理功能规格 |
| API 文档 | [004-dept/api.md](./004-dept/api.md) | 部门管理 API 接口定义 |
| 数据模型 | [004-dept/data-model.md](./004-dept/data-model.md) | 部门实体与类型定义 |
| 页面清单 | [004-dept/pages.md](./004-dept/pages.md) | 部门管理页面路由与组件清单 |
| 用户故事 | [004-dept/user-stories.md](./004-dept/user-stories.md) | 部门管理详细用户故事 |

---

### 005 — 岗位管理 (Post)

> 岗位的管理配置：用户所属岗位职责定义。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [005-post/spec.md](./005-post/spec.md) | 岗位管理功能规格 |
| API 文档 | [005-post/api.md](./005-post/api.md) | 岗位管理 API 接口定义 |
| 数据模型 | [005-post/data-model.md](./005-post/data-model.md) | 岗位实体与类型定义 |
| 页面清单 | [005-post/pages.md](./005-post/pages.md) | 岗位管理页面路由与组件清单 |
| 用户故事 | [005-post/user-stories.md](./005-post/user-stories.md) | 岗位管理详细用户故事 |

---

### 006 — 菜单管理 (Menu)

> 系统菜单、按钮权限、路由配置管理。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [006-menu/spec.md](./006-menu/spec.md) | 菜单管理功能规格 |
| API 文档 | [006-menu/api.md](./006-menu/api.md) | 菜单管理 API 接口定义 |
| 数据模型 | [006-menu/data-model.md](./006-menu/data-model.md) | 菜单实体与类型定义 |
| 页面清单 | [006-menu/pages.md](./006-menu/pages.md) | 菜单管理页面路由与组件清单 |
| 用户故事 | [006-menu/user-stories.md](./006-menu/user-stories.md) | 菜单管理详细用户故事 |

---

### 007 — 字典管理 (Dict)

> 系统字典数据管理：字典类型、字典数据维护。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [007-dict/spec.md](./007-dict/spec.md) | 字典管理功能规格 |
| API 文档 | [007-dict/api.md](./007-dict/api.md) | 字典管理 API 接口定义 |
| 数据模型 | [007-dict/data-model.md](./007-dict/data-model.md) | 字典实体与类型定义 |
| 页面清单 | [007-dict/pages.md](./007-dict/pages.md) | 字典管理页面路由与组件清单 |
| 用户故事 | [007-dict/user-stories.md](./007-dict/user-stories.md) | 字典管理详细用户故事 |

---

### 008 — 参数配置 (Config)

> 系统参数配置管理：动态配置系统运行参数。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [008-config/spec.md](./008-config/spec.md) | 参数配置功能规格 |
| API 文档 | [008-config/api.md](./008-config/api.md) | 参数配置 API 接口定义 |
| 数据模型 | [008-config/data-model.md](./008-config/data-model.md) | 参数配置实体与类型定义 |
| 页面清单 | [008-config/pages.md](./008-config/pages.md) | 参数配置页面路由与组件清单 |
| 用户故事 | [008-config/user-stories.md](./008-config/user-stories.md) | 参数配置详细用户故事 |

---

### 009 — 通知公告 (Notice)

> 系统通知公告发布与管理。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [009-notice/spec.md](./009-notice/spec.md) | 通知公告功能规格 |
| API 文档 | [009-notice/api.md](./009-notice/api.md) | 通知公告 API 接口定义 |
| 数据模型 | [009-notice/data-model.md](./009-notice/data-model.md) | 通知公告实体与类型定义 |
| 页面清单 | [009-notice/pages.md](./009-notice/pages.md) | 通知公告页面路由与组件清单 |
| 用户故事 | [009-notice/user-stories.md](./009-notice/user-stories.md) | 通知公告详细用户故事 |

---

### 010 — 操作日志 (OperLog)

> 系统操作日志记录与查询。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [010-oper-log/spec.md](./010-oper-log/spec.md) | 操作日志功能规格 |
| API 文档 | [010-oper-log/api.md](./010-oper-log/api.md) | 操作日志 API 接口定义 |
| 数据模型 | [010-oper-log/data-model.md](./010-oper-log/data-model.md) | 操作日志实体与类型定义 |
| 页面清单 | [010-oper-log/pages.md](./010-oper-log/pages.md) | 操作日志页面路由与组件清单 |
| 用户故事 | [010-oper-log/user-stories.md](./010-oper-log/user-stories.md) | 操作日志详细用户故事 |

---

### 011 — 登录日志 (LoginLog)

> 系统登录日志记录与查询，包含登录异常记录。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [011-login-log/spec.md](./011-login-log/spec.md) | 登录日志功能规格 |
| API 文档 | [011-login-log/api.md](./011-login-log/api.md) | 登录日志 API 接口定义 |
| 数据模型 | [011-login-log/data-model.md](./011-login-log/data-model.md) | 登录日志实体与类型定义 |
| 页面清单 | [011-login-log/pages.md](./011-login-log/pages.md) | 登录日志页面路由与组件清单 |
| 用户故事 | [011-login-log/user-stories.md](./011-login-log/user-stories.md) | 登录日志详细用户故事 |

---

## 四、系统监控模块（012 ~ 016）

### 012 — 在线用户 (Online)

> 在线用户监控与管理，支持强制踢出操作。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [012-online/spec.md](./012-online/spec.md) | 在线用户功能规格 |
| API 文档 | [012-online/api.md](./012-online/api.md) | 在线用户 API 接口定义 |
| 数据模型 | [012-online/data-model.md](./012-online/data-model.md) | 在线用户实体与类型定义 |
| 页面清单 | [012-online/pages.md](./012-online/pages.md) | 在线用户页面路由与组件清单 |
| 用户故事 | [012-online/user-stories.md](./012-online/user-stories.md) | 在线用户详细用户故事 |

---

### 013 — 定时任务 (Job)

> 定时任务调度管理，支持运行报表、任务管理、执行器管理等。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [013-job/spec.md](./013-job/spec.md) | 定时任务功能规格 |
| API 文档 | [013-job/api.md](./013-job/api.md) | 定时任务 API 接口定义 |
| 数据模型 | [013-job/data-model.md](./013-job/data-model.md) | 定时任务实体与类型定义 |
| 页面清单 | [013-job/pages.md](./013-job/pages.md) | 定时任务页面路由与组件清单 |
| 用户故事 | [013-job/user-stories.md](./013-job/user-stories.md) | 定时任务详细用户故事 |

---

### 014 — 缓存管理 (Cache)

> 系统缓存信息查询与命令统计。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [014-cache/spec.md](./014-cache/spec.md) | 缓存管理功能规格 |
| API 文档 | [014-cache/api.md](./014-cache/api.md) | 缓存管理 API 接口定义 |
| 数据模型 | [014-cache/data-model.md](./014-cache/data-model.md) | 缓存管理实体与类型定义 |
| 页面清单 | [014-cache/pages.md](./014-cache/pages.md) | 缓存管理页面路由与组件清单 |
| 用户故事 | [014-cache/user-stories.md](./014-cache/user-stories.md) | 缓存管理详细用户故事 |

---

### 015 — 服务监控 (Server)

> 系统服务监控：CPU、内存、磁盘、堆栈、在线日志等。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [015-server/spec.md](./015-server/spec.md) | 服务监控功能规格 |
| API 文档 | [015-server/api.md](./015-server/api.md) | 服务监控 API 接口定义 |
| 数据模型 | [015-server/data-model.md](./015-server/data-model.md) | 服务监控实体与类型定义 |
| 页面清单 | [015-server/pages.md](./015-server/pages.md) | 服务监控页面路由与组件清单 |
| 用户故事 | [015-server/user-stories.md](./015-server/user-stories.md) | 服务监控详细用户故事 |

---

### 016 — 连接池监控 (Pool)

> 数据库连接池监控与管理。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [016-pool/spec.md](./016-pool/spec.md) | 连接池监控功能规格 |
| API 文档 | [016-pool/api.md](./016-pool/api.md) | 连接池监控 API 接口定义 |
| 数据模型 | [016-pool/data-model.md](./016-pool/data-model.md) | 连接池监控实体与类型定义 |
| 页面清单 | [016-pool/pages.md](./016-pool/pages.md) | 连接池监控页面路由与组件清单 |
| 用户故事 | [016-pool/user-stories.md](./016-pool/user-stories.md) | 连接池监控详细用户故事 |

---

## 五、系统工具模块（017 ~ 019）

### 017 — 代码生成 (Gen)

> 多数据源前后端代码生成，支持 CRUD 下载。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [017-gen/spec.md](./017-gen/spec.md) | 代码生成功能规格 |
| API 文档 | [017-gen/api.md](./017-gen/api.md) | 代码生成 API 接口定义 |
| 数据模型 | [017-gen/data-model.md](./017-gen/data-model.md) | 代码生成实体与类型定义 |
| 页面清单 | [017-gen/pages.md](./017-gen/pages.md) | 代码生成页面路由与组件清单 |
| 用户故事 | [017-gen/user-stories.md](./017-gen/user-stories.md) | 代码生成详细用户故事 |

---

### 018 — API 文档 (Swagger)

> 系统接口文档自动生成，基于 SpringDoc。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [018-swagger/spec.md](./018-swagger/spec.md) | API 文档功能规格 |
| API 文档 | [018-swagger/api.md](./018-swagger/api.md) | API 文档接口定义 |
| 数据模型 | [018-swagger/data-model.md](./018-swagger/data-model.md) | API 文档实体与类型定义 |
| 页面清单 | [018-swagger/pages.md](./018-swagger/pages.md) | API 文档页面路由与组件清单 |
| 用户故事 | [018-swagger/user-stories.md](./018-swagger/user-stories.md) | API 文档详细用户故事 |

---

### 019 — 构建工具 (Build)

> 项目构建相关工具与配置。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [019-build/spec.md](./019-build/spec.md) | 构建工具功能规格 |
| API 文档 | [019-build/api.md](./019-build/api.md) | 构建工具 API 接口定义 |
| 数据模型 | [019-build/data-model.md](./019-build/data-model.md) | 构建工具实体与类型定义 |
| 页面清单 | [019-build/pages.md](./019-build/pages.md) | 构建工具页面路由与组件清单 |
| 用户故事 | [019-build/user-stories.md](./019-build/user-stories.md) | 构建工具详细用户故事 |

---

## 六、Plus 增强模块（feature-101 ~ feature-113）

### feature-101 — 工作流 (Workflow)

> 工作流管理，支持各种复杂审批、转办、委派、加减签、会签、或签、票签等。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-101-workflow/spec.md](./feature-101-workflow/spec.md) | 工作流功能规格 |
| API 文档 | [feature-101-workflow/api.md](./feature-101-workflow/api.md) | 工作流 API 接口定义 |
| 数据模型 | [feature-101-workflow/data-model.md](./feature-101-workflow/data-model.md) | 工作流实体与类型定义 |
| 页面清单 | [feature-101-workflow/pages.md](./feature-101-workflow/pages.md) | 工作流页面路由与组件清单 |
| 用户故事 | [feature-101-workflow/user-stories.md](./feature-101-workflow/user-stories.md) | 工作流详细用户故事 |

---

### feature-102 — 示例模块 (Demo)

> 系统功能示例与案例展示。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-102-demo/spec.md](./feature-102-demo/spec.md) | 示例模块功能规格 |
| API 文档 | [feature-102-demo/api.md](./feature-102-demo/api.md) | 示例模块 API 接口定义 |
| 数据模型 | [feature-102-demo/data-model.md](./feature-102-demo/data-model.md) | 示例模块实体与类型定义 |
| 页面清单 | [feature-102-demo/pages.md](./feature-102-demo/pages.md) | 示例模块页面路由与组件清单 |
| 用户故事 | [feature-102-demo/user-stories.md](./feature-102-demo/user-stories.md) | 示例模块详细用户故事 |

---

### feature-103 — 对象存储 (OSS)

> 文件管理：文件展示、上传、下载、删除等，支持 MinIO 和 S3 协议。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-103-oss/spec.md](./feature-103-oss/spec.md) | 对象存储功能规格 |
| API 文档 | [feature-103-oss/api.md](./feature-103-oss/api.md) | 对象存储 API 接口定义 |
| 数据模型 | [feature-103-oss/data-model.md](./feature-103-oss/data-model.md) | 对象存储实体与类型定义 |
| 页面清单 | [feature-103-oss/pages.md](./feature-103-oss/pages.md) | 对象存储页面路由与组件清单 |
| 用户故事 | [feature-103-oss/user-stories.md](./feature-103-oss/user-stories.md) | 对象存储详细用户故事 |

---

### feature-104 — 加密模块 (Encrypt)

> 数据加解密支持，支持 BASE64、AES、RSA、SM2、SM4 等策略。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-104-encrypt/spec.md](./feature-104-encrypt/spec.md) | 加密模块功能规格 |
| API 文档 | [feature-104-encrypt/api.md](./feature-104-encrypt/api.md) | 加密模块 API 接口定义 |
| 数据模型 | [feature-104-encrypt/data-model.md](./feature-104-encrypt/data-model.md) | 加密模块实体与类型定义 |
| 页面清单 | [feature-104-encrypt/pages.md](./feature-104-encrypt/pages.md) | 加密模块页面路由与组件清单 |
| 用户故事 | [feature-104-encrypt/user-stories.md](./feature-104-encrypt/user-stories.md) | 加密模块详细用户故事 |

---

### feature-105 — 数据脱敏 (Sensitive)

> 数据脱敏支持，支持身份证、手机号、地址、邮箱、银行卡等策略。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-105-sensitive/spec.md](./feature-105-sensitive/spec.md) | 数据脱敏功能规格 |
| API 文档 | [feature-105-sensitive/api.md](./feature-105-sensitive/api.md) | 数据脱敏 API 接口定义 |
| 数据模型 | [feature-105-sensitive/data-model.md](./feature-105-sensitive/data-model.md) | 数据脱敏实体与类型定义 |
| 页面清单 | [feature-105-sensitive/pages.md](./feature-105-sensitive/pages.md) | 数据脱敏页面路由与组件清单 |
| 用户故事 | [feature-105-sensitive/user-stories.md](./feature-105-sensitive/user-stories.md) | 数据脱敏详细用户故事 |

---

### feature-106 — 幂等控制 (Idempotent)

> 分布式幂等控制，参考美团 GTIS 防重系统简化实现。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-106-idempotent/spec.md](./feature-106-idempotent/spec.md) | 幂等控制功能规格 |
| API 文档 | [feature-106-idempotent/api.md](./feature-106-idempotent/api.md) | 幂等控制 API 接口定义 |
| 数据模型 | [feature-106-idempotent/data-model.md](./feature-106-idempotent/data-model.md) | 幂等控制实体与类型定义 |
| 页面清单 | [feature-106-idempotent/pages.md](./feature-106-idempotent/pages.md) | 幂等控制页面路由与组件清单 |
| 用户故事 | [feature-106-idempotent/user-stories.md](./feature-106-idempotent/user-stories.md) | 幂等控制详细用户故事 |

---

### feature-107 — 限流控制 (Ratelimiter)

> 分布式限流控制，基于 Redis 的限流实现。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-107-ratelimiter/spec.md](./feature-107-ratelimiter/spec.md) | 限流控制功能规格 |
| API 文档 | [feature-107-ratelimiter/api.md](./feature-107-ratelimiter/api.md) | 限流控制 API 接口定义 |
| 数据模型 | [feature-107-ratelimiter/data-model.md](./feature-107-ratelimiter/data-model.md) | 限流控制实体与类型定义 |
| 页面清单 | [feature-107-ratelimiter/pages.md](./feature-107-ratelimiter/pages.md) | 限流控制页面路由与组件清单 |
| 用户故事 | [feature-107-ratelimiter/user-stories.md](./feature-107-ratelimiter/user-stories.md) | 限流控制详细用户故事 |

---

### feature-108 — 短信服务 (SMS)

> 短信发送服务，支持数十种短信厂家，可多厂家共用。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-108-sms/spec.md](./feature-108-sms/spec.md) | 短信服务功能规格 |
| API 文档 | [feature-108-sms/api.md](./feature-108-sms/api.md) | 短信服务 API 接口定义 |
| 数据模型 | [feature-108-sms/data-model.md](./feature-108-sms/data-model.md) | 短信服务实体与类型定义 |
| 页面清单 | [feature-108-sms/pages.md](./feature-108-sms/pages.md) | 短信服务页面路由与组件清单 |
| 用户故事 | [feature-108-sms/user-stories.md](./feature-108-sms/user-stories.md) | 短信服务详细用户故事 |

---

### feature-109 — 社交登录 (Social)

> 第三方社交登录支持，支持微信、钉钉等数十种三方认证。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-109-social/spec.md](./feature-109-social/spec.md) | 社交登录功能规格 |
| API 文档 | [feature-109-social/api.md](./feature-109-social/api.md) | 社交登录 API 接口定义 |
| 数据模型 | [feature-109-social/data-model.md](./feature-109-social/data-model.md) | 社交登录实体与类型定义 |
| 页面清单 | [feature-109-social/pages.md](./feature-109-social/pages.md) | 社交登录页面路由与组件清单 |
| 用户故事 | [feature-109-social/user-stories.md](./feature-109-social/user-stories.md) | 社交登录详细用户故事 |

---

### feature-110 — 邮件服务 (Mail)

> 邮件发送服务，支持大部分邮件厂商通用协议。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-110-mail/spec.md](./feature-110-mail/spec.md) | 邮件服务功能规格 |
| API 文档 | [feature-110-mail/api.md](./feature-110-mail/api.md) | 邮件服务 API 接口定义 |
| 数据模型 | [feature-110-mail/data-model.md](./feature-110-mail/data-model.md) | 邮件服务实体与类型定义 |
| 页面清单 | [feature-110-mail/pages.md](./feature-110-mail/pages.md) | 邮件服务页面路由与组件清单 |
| 用户故事 | [feature-110-mail/user-stories.md](./feature-110-mail/user-stories.md) | 邮件服务详细用户故事 |

---

### feature-111 — 分布式任务 (SnailJob)

> 分布式任务调度，支持分片、重试、DAG 任务流等。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-111-snailjob/spec.md](./feature-111-snailjob/spec.md) | 分布式任务功能规格 |
| API 文档 | [feature-111-snailjob/api.md](./feature-111-snailjob/api.md) | 分布式任务 API 接口定义 |
| 数据模型 | [feature-111-snailjob/data-model.md](./feature-111-snailjob/data-model.md) | 分布式任务实体与类型定义 |
| 页面清单 | [feature-111-snailjob/pages.md](./feature-111-snailjob/pages.md) | 分布式任务页面路由与组件清单 |
| 用户故事 | [feature-111-snailjob/user-stories.md](./feature-111-snailjob/user-stories.md) | 分布式任务详细用户故事 |

---

### feature-112 — WebSocket

> WebSocket 实时通信支持，扩展了 Token 鉴权与分布式会话同步。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-112-websocket/spec.md](./feature-112-websocket/spec.md) | WebSocket 功能规格 |
| API 文档 | [feature-112-websocket/api.md](./feature-112-websocket/api.md) | WebSocket API 接口定义 |
| 数据模型 | [feature-112-websocket/data-model.md](./feature-112-websocket/data-model.md) | WebSocket 实体与类型定义 |
| 页面清单 | [feature-112-websocket/pages.md](./feature-112-websocket/pages.md) | WebSocket 页面路由与组件清单 |
| 用户故事 | [feature-112-websocket/user-stories.md](./feature-112-websocket/user-stories.md) | WebSocket 详细用户故事 |

---

### feature-113 — SSE 推送

> SSE 服务器发送事件支持，扩展了 Token 鉴权与分布式会话同步。

| 文档 | 链接 | 说明 |
|------|------|------|
| 功能规格 | [feature-113-sse/spec.md](./feature-113-sse/spec.md) | SSE 推送功能规格 |
| API 文档 | [feature-113-sse/api.md](./feature-113-sse/api.md) | SSE 推送 API 接口定义 |
| 数据模型 | [feature-113-sse/data-model.md](./feature-113-sse/data-model.md) | SSE 推送实体与类型定义 |
| 页面清单 | [feature-113-sse/pages.md](./feature-113-sse/pages.md) | SSE 推送页面路由与组件清单 |
| 用户故事 | [feature-113-sse/user-stories.md](./feature-113-sse/user-stories.md) | SSE 推送详细用户故事 |

---

## 七、模块编号一览

| 编号 | 模块名 | 英文名 | 分类 |
|------|--------|--------|------|
| 001 | 租户管理 | Tenant | 系统管理 |
| 002 | 用户管理 | User | 系统管理 |
| 003 | 角色管理 | Role | 系统管理 |
| 004 | 部门管理 | Dept | 系统管理 |
| 005 | 岗位管理 | Post | 系统管理 |
| 006 | 菜单管理 | Menu | 系统管理 |
| 007 | 字典管理 | Dict | 系统管理 |
| 008 | 参数配置 | Config | 系统管理 |
| 009 | 通知公告 | Notice | 系统管理 |
| 010 | 操作日志 | OperLog | 系统监控 |
| 011 | 登录日志 | LoginLog | 系统监控 |
| 012 | 在线用户 | Online | 系统监控 |
| 013 | 定时任务 | Job | 系统监控 |
| 014 | 缓存管理 | Cache | 系统监控 |
| 015 | 服务监控 | Server | 系统监控 |
| 016 | 连接池监控 | Pool | 系统监控 |
| 017 | 代码生成 | Gen | 系统工具 |
| 018 | API 文档 | Swagger | 系统工具 |
| 019 | 构建工具 | Build | 系统工具 |
| 101 | 工作流管理 | Workflow | Plus 增强 |
| 102 | 示例模块 | Demo | Plus 增强 |
| 103 | 对象存储 | OSS | Plus 增强 |
| 104 | 加密模块 | Encrypt | Plus 增强 |
| 105 | 数据脱敏 | Sensitive | Plus 增强 |
| 106 | 幂等控制 | Idempotent | Plus 增强 |
| 107 | 限流控制 | Ratelimiter | Plus 增强 |
| 108 | 短信服务 | SMS | Plus 增强 |
| 109 | 社交登录 | Social | Plus 增强 |
| 110 | 邮件服务 | Mail | Plus 增强 |
| 111 | 分布式任务 | SnailJob | Plus 增强 |
| 112 | WebSocket | WebSocket | Plus 增强 |
| 113 | SSE 推送 | SSE | Plus 增强 |

---

## 八、模块文档结构规范

每个模块目录 `NNN-name/` 或 `feature-NNN-name/` 下包含以下 5 份标准文档：

| 文件 | 命名 | 说明 |
|------|------|------|
| 功能规格 | `spec.md` | 定义模块的功能需求、用户故事、验收标准 |
| API 文档 | `api.md` | 模块涉及的 API 接口定义、请求/响应格式 |
| 数据模型 | `data-model.md` | 模块所需的实体、类型、枚举定义 |
| 页面清单 | `pages.md` | 模块包含的页面路由、组件树、交互流程 |
| 用户故事 | `user-stories.md` | 模块详细用户故事和场景描述 |

---

## 九、快速导航

| 目标读者 | 推荐阅读顺序 |
|---------|-------------|
| **新加入开发者** | constitution.md → STRUCTURE.md → overall-spec.md → 具体模块 spec.md |
| **架构师 / Tech Lead** | ARCHITECTURE.md → TECH.md → overall-plan.md → overall-api.md |
| **后端开发** | overall-api.md → overall-data-model.md → 对应模块的 api.md + spec.md |
| **前端开发** | STRUCTURE.md → 对应模块的 spec.md + api.md + pages.md |
| **测试 / QA** | SPECS_CHECKLIST.md → overall-spec.md → 各模块 spec.md |
| **产品经理** | overall-spec.md → 对应模块 spec.md |

---

**文档维护者：** RuoYi-Vue-Plus 开发团队  
**文档仓库：** `RuoYi-Vue-Plus/specs/`  
**最后更新：** 2026-05-05
