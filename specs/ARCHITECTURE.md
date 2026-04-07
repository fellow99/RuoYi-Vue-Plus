# 整体架构文档 (ARCHITECTURE.md)

**版本：** 5.5.3  
**最后更新：** 2026-03-13  
**项目：** RuoYi-Vue-Plus

---

## 一、系统架构概述

### 1.1 架构风格

RuoYi-Vue-Plus 采用**前后端分离的分层架构 + 插件化模块设计 + 多租户架构**：

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
│            (Spring Boot 3.5 + Sa-Token + JWT)                │
│  ┌─────────────┬─────────────┬─────────────┬─────────────┐  │
│  │  Controller │   Service   │   Mapper    │   Entity    │  │
│  │    层       │    层       │    层       │    层       │  │
│  └─────────────┴─────────────┴─────────────┴─────────────┘  │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
         ┌─────────┐    ┌─────────┐    ┌─────────┐
         │  MySQL  │    │  Redis  │    │  MinIO  │
         │ 数据库   │    │  缓存   │    │  文件   │
         └─────────┘    └─────────┘    └─────────┘
```

### 1.2 架构特点

- **前后端分离：** 前端负责展示交互，后端负责业务逻辑和数据
- **RESTful API：** 标准化接口设计，易于维护和扩展
- **无状态认证：** JWT Token 认证，支持水平扩展
- **插件化模块：** 多模块 Maven 项目，插件化设计，职责清晰
- **多租户支持：** 基于 MyBatis-Plus 插件的无感多租户数据隔离
- **AOP 切面：** 统一处理日志、权限、事务

---

## 二、系统分层架构

### 2.1 四层架构模型

```
┌────────────────────────────────────────────────────────────┐
│                    Controller 层                            │
│  • 接收 HTTP 请求                                            │
│  • 参数校验                                                 │
│  • 调用 Service 层                                          │
│  • 返回统一响应格式                                         │
└────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────┐
│                     Service 层                              │
│  • 业务逻辑实现                                             │
│  • 事务控制                                                 │
│  • 调用 Mapper 层                                           │
│  • 数据组装                                                 │
└────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────┐
│                     Mapper 层                               │
│  • 数据库访问                                               │
│  • SQL 执行                                                 │
│  • 结果集映射                                               │
└────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────┐
│                     Entity 层                               │
│  • 数据实体定义                                             │
│  • 数据库表映射                                             │
│  • 数据传输对象                                             │
└────────────────────────────────────────────────────────────┘
```

### 2.2 各层职责详解

#### Controller 层
- **位置：** `ruoyi-admin/src/main/java/org/dromara/web/controller/`
- **职责：**
  - 接收和解析 HTTP 请求
  - 参数验证（使用 Validation 注解）
  - 调用 Service 层处理业务
  - 封装统一响应结果（R 响应对象）
  - 处理异常（全局异常处理器）

#### Service 层
- **位置：** `ruoyi-modules/ruoyi-system/src/main/java/org/dromara/ruoyi/system/service/`
- **职责：**
  - 实现核心业务逻辑
  - 事务管理（@Transactional）
  - 数据校验和业务规则
  - 调用一个或多个 Mapper
  - 数据转换和组装（使用 MapStruct）

#### Mapper 层
- **位置：** `ruoyi-modules/ruoyi-system/src/main/java/org/dromara/ruoyi/system/mapper/`
- **职责：**
  - 定义数据库访问接口
  - 执行 SQL 语句（MP 注解或 XML）
  - 结果集映射到实体类
  - 支持动态 SQL（MyBatis）

#### Entity 层
- **位置：** `ruoyi-modules/ruoyi-system/src/main/java/org/dromara/ruoyi/system/domain/`
- **职责：**
  - 定义数据实体类
  - 数据库表字段映射（@TableName）
  - 支持链式调用和 Builder 模式
  - 包含 DTO、VO、DO 等变体

---

## 三、模块依赖关系

### 3.1 模块结构

```
RuoYi-Vue-Plus/
├── ruoyi-admin/           # 主启动模块（入口）
├── ruoyi-common/          # 通用模块（公共代码）
├── ruoyi-extend/          # 扩展模块（监控、SnailJob 等）
├── ruoyi-modules/         # 业务模块（系统、代码生成、任务、工作流等）
└── script/                # 脚本目录（SQL、Docker 等）
```

### 3.2 通用模块结构 (ruoyi-common)

```
ruoyi-common/
├── ruoyi-common-bom/          # 依赖管理
├── ruoyi-common-core/         # 核心工具类
├── ruoyi-common-security/     # 安全认证
├── ruoyi-common-satoken/      # Sa-Token 集成
├── ruoyi-common-mybatis/      # MyBatis-Plus 集成
├── ruoyi-common-redis/        # Redis 集成
├── ruoyi-common-web/          # Web 配置
├── ruoyi-common-doc/          # 接口文档
├── ruoyi-common-json/         # JSON 配置
├── ruoyi-common-log/          # 日志记录
├── ruoyi-common-tenant/       # 多租户支持
├── ruoyi-common-encrypt/      # 数据加密
├── ruoyi-common-sensitive/    # 数据脱敏
├── ruoyi-common-translation/  # 数据翻译
├── ruoyi-common-excel/        # Excel 处理
├── ruoyi-common-idempotent/   # 幂等性
├── ruoyi-common-ratelimiter/  # 限流
├── ruoyi-common-job/          # 任务调度
├── ruoyi-common-oss/          # 对象存储
├── ruoyi-common-sms/          # 短信
├── ruoyi-common-social/       # 社交登录
├── ruoyi-common-mail/         # 邮件
├── ruoyi-common-sse/          # SSE 推送
└── ruoyi-common-websocket/    # WebSocket
```

### 3.3 业务模块结构 (ruoyi-modules)

```
ruoyi-modules/
├── ruoyi-system/          # 系统业务模块（用户、部门、角色、菜单等）
├── ruoyi-generator/       # 代码生成模块
├── ruoyi-job/             # 定时任务模块（SnailJob）
├── ruoyi-workflow/        # 工作流模块（Warm-Flow）
└── ruoyi-demo/            # Demo 示例模块
```

### 3.4 扩展模块结构 (ruoyi-extend)

```
ruoyi-extend/
├── ruoyi-monitor-admin/   # Spring Boot Admin 监控
└── ruoyi-snailjob-server/ # SnailJob 服务端
```

### 3.5 依赖关系图

```
                    ┌─────────────────┐
                    │   ruoyi-admin   │
                    │   (主启动模块)   │
                    └────────┬────────┘
                             │
            ┌────────────────┼────────────────┐
            │                │                │
            ▼                ▼                ▼
    ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
    │ ruoyi-common │ │ruoyi-modules │ │ ruoyi-extend │
    │  (通用模块)   │ │ (业务模块)    │ │  (扩展模块)   │
    └──────────────┘ └──────────────┘ └──────────────┘
```

### 3.6 依赖关系表

| 模块 | 依赖 |
|------|------|
| ruoyi-admin | ruoyi-common-web, ruoyi-modules/*, ruoyi-extend/* |
| ruoyi-system | ruoyi-common-web, ruoyi-common-mybatis |
| ruoyi-generator | ruoyi-common-web, ruoyi-common-mybatis |
| ruoyi-job | ruoyi-common-job, ruoyi-common-web |
| ruoyi-workflow | ruoyi-common-web, ruoyi-common-mybatis |
| ruoyi-demo | ruoyi-common-web, ruoyi-common-mybatis |

---

## 四、数据流向

### 4.1 请求处理流程

```
用户请求 → Nginx → Controller → Service → Mapper → Database
                                    ↓
                                Redis 缓存
                                    ↓
                              返回响应数据
```

### 4.2 认证授权流程

```
用户登录 → 验证 credentials → 生成 JWT Token → 返回 Token
     ↓
携带 Token 请求 → Sa-Token 拦截器 → 解析 Token → 获取用户信息
     ↓
权限校验 (@SaCheckRole/@SaCheckPermission) → 执行业务逻辑
```

### 4.3 多租户数据隔离流程

```
请求进入 → 解析租户标识 (header/param) → 租户上下文设置
     ↓
Service 层业务处理 → Mapper 层查询
     ↓
MyBatis-Plus 多租户插件 → 自动添加 tenant_id 条件
     ↓
数据库查询 (仅返回当前租户数据)
```

---

## 五、多租户架构设计

### 5.1 租户隔离方案

RuoYi-Vue-Plus 采用**共享数据库 + 共享表 + tenant_id 字段隔离**方案：

```
┌─────────────────────────────────────────────────────────┐
│                    数据库 (MySQL)                        │
│  ┌─────────────────────────────────────────────────┐    │
│  │  sys_user 表                                     │    │
│  │  +----+----------+----------+--------+          │    │
│  │  │ id │ username │ tenant_id│  ...   │          │    │
│  │  +----+----------+----------+--------+          │    │
│  │  │ 1  │ admin    │ 1001     │  ...   │          │    │
│  │  │ 2  │ user1    │ 1001     │  ...   │          │    │
│  │  │ 3  │ user2    │ 1002     │  ...   │          │    │
│  │  +----+----------+----------+--------+          │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
```

### 5.2 租户上下文管理

```java
// 租户信息自动注入
TenantContextHolder.setTenantId(1001L);

// MyBatis-Plus 多租户插件自动处理
// SELECT * FROM sys_user WHERE tenant_id = 1001
```

### 5.3 租户管理功能

- **租户开通：** 创建租户账号、分配套餐
- **租户套餐：** 限制用户数量、功能权限
- **租户过期：** 自动提醒、服务暂停
- **数据隔离：** 租户间数据完全隔离

---

## 六、权限架构设计

### 6.1 权限模型

RuoYi-Vue-Plus 采用 **RBAC + 数据权限** 模型：

```
用户 (User) ←→ 角色 (Role) ←→ 菜单权限 (Menu)
                      ↓
                数据权限 (DataScope)
                      ↓
                部门/数据范围过滤
```

### 6.2 权限类型

| 权限类型 | 说明 | 实现方式 |
|----------|------|----------|
| 登录认证 | 用户身份验证 | Sa-Token + JWT |
| 菜单权限 | 控制菜单可见性 | 前端路由守卫 + 后端接口权限 |
| 按钮权限 | 控制操作按钮显示 | `@SaCheckPermission` 注解 |
| 数据权限 | 控制数据访问范围 | 数据范围过滤 + AOP |
| 角色权限 | 基于角色的权限控制 | `@SaCheckRole` 注解 |

### 6.3 权限注解使用

```java
// 角色校验
@SaCheckRole("admin")

// 权限校验
@SaCheckPermission("system:user:add")

// 复合权限 (AND)
@SaCheckPermission(value = {"system:user:add", "system:user:edit"}, mode = SaMode.AND)

// 复合权限 (OR)
@SaCheckPermission(value = {"system:user:add", "system:user:edit"}, mode = SaMode.OR)

// 登录校验
@SaCheckLogin

// 二级认证
@SaCheckSafe
```

### 6.4 数据权限范围

| 范围 | 说明 | SQL 过滤条件 |
|------|------|--------------|
| 全部数据 | 可查看所有数据 | 无过滤 |
| 本部门及以下 | 查看本部门及下级部门数据 | `dept_id IN (当前部门，下级部门)` |
| 本部门 | 仅查看本部门数据 | `dept_id = 当前部门` |
| 仅本人 | 仅查看自己创建的数据 | `create_by = 当前用户` |
| 自定义 | 按角色自定义数据范围 | 按配置过滤 |

---

## 七、分布式架构支持

### 7.1 分布式会话

- **Redisson 会话管理：** 会话数据存储在 Redis
- **集群共享：** 多节点共享会话，支持水平扩展
- **Token 机制：** JWT 无状态认证，不依赖服务端会话

### 7.2 分布式锁

```java
// 基于 Lock4j 的分布式锁
@Lock4j(keys = {"#userId"}, expire = 30000, acquireTimeout = 3000)
public void updateUser(Long userId, ...) {
    // 业务逻辑
}
```

### 7.3 分布式任务调度

- **SnailJob 统一管理：** 集中式任务调度中心
- **分片执行：** 任务分片，多节点并行执行
- **失败重试：** 自动重试机制
- **DAG 任务流：** 支持任务依赖关系

### 7.4 分布式文件存储

- **MinIO 集群：** 多节点、多硬盘、多副本
- **S3 协议：** 兼容 AWS S3、七牛、阿里、腾讯等
- **分片上传：** 支持大文件分片上传

---

## 八、安全架构

### 8.1 认证安全

- **JWT Token：** 无状态认证，支持过期时间
- **Refresh Token：** 支持 Token 刷新机制
- **二级认证：** 敏感操作需二次验证
- **单点登录：** 支持 SSO 集成

### 8.2 接口安全

- **传输加密：** 动态 AES + RSA 加密请求体
- **签名验证：** 请求签名防篡改
- **幂等控制：** 防止重复提交
- **限流控制：** 防止恶意请求

### 8.3 数据安全

- **数据脱敏：** 敏感数据自动脱敏展示
- **数据加密：** 敏感数据加密存储
- **SQL 防注入：** 参数化查询
- **XSS 防护：** 输入输出过滤

---

## 九、监控与运维

### 9.1 服务监控

- **Spring Boot Admin：** 服务状态监控
- **在线日志：** 实时查看服务日志
- **健康检查：** 服务健康状态探针

### 9.2 链路追踪

- **SkyWalking：** 分布式链路追踪
- **性能分析：** 请求耗时分析
- **故障定位：** 快速定位问题节点

### 9.3 日志管理

- **统一日志：** 标准化日志格式
- **日志级别：** 支持动态调整日志级别
- **日志审计：** 操作日志记录与审计

---

**文档版本：** 1.0  
**维护者：** 开发团队  
**最后更新：** 2026-03-13
