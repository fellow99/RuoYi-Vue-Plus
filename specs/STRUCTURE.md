# RuoYi-Vue-Plus 项目结构文档

**版本：** 5.5.3  
**生成时间：** 2026-03-13  
**参考工程：** RuoYi-Vue 3.9.1

---

## 一、项目概述

RuoYi-Vue-Plus 是基于 RuoYi-Vue 框架全方位重写的多租户管理系统，针对分布式集群与多租户场景进行了升级。

### 1.1 核心差异

| 特性 | RuoYi-Vue | RuoYi-Vue-Plus |
|------|-----------|----------------|
| Spring Boot | 2.x | 3.5.9 |
| JDK | 8/11 | 17/21 |
| 权限框架 | Spring Security | Sa-Token |
| ORM | MyBatis (XML) | MyBatis-Plus (注解) |
| Redis 客户端 | Lettuce + RedisTemplate | Redisson |
| 序列化 | FastJSON | Jackson |
| Web 容器 | Tomcat | Undertow |
| 多租户 | ❌ | ✅ |
| 数据脱敏 | ❌ | ✅ |
| 数据加密 | ❌ | ✅ |
| 分布式锁 | ❌ | ✅ (Lock4j) |
| 分布式任务 | Quartz | SnailJob |
| 文件存储 | 本地文件 | MinIO + S3 |
| 工作流 | ❌ | ✅ (Warm-Flow) |

---

## 二、目录结构

```
RuoYi-Vue-Plus/
├── ruoyi-admin/                    # 主启动模块
│   └── src/main/java/
│       └── org/dromara/ruoyi/
│           ├── RuoYiApplication.java    # 启动类
│           └── web/                     # Controller 层
│
├── ruoyi-common/                   # 通用模块集合
│   ├── ruoyi-common-bom/           # 依赖管理
│   ├── ruoyi-common-core/          # 核心工具类
│   ├── ruoyi-common-doc/           # 接口文档配置
│   ├── ruoyi-common-encrypt/       # 数据加解密
│   ├── ruoyi-common-excel/         # Excel 处理
│   ├── ruoyi-common-idempotent/    # 幂等控制
│   ├── ruoyi-common-job/           # 任务调度
│   ├── ruoyi-common-json/          # JSON 配置
│   ├── ruoyi-common-log/           # 日志处理
│   ├── ruoyi-common-mail/          # 邮件发送
│   ├── ruoyi-common-mybatis/       # MyBatis 配置
│   ├── ruoyi-common-oss/           # 对象存储
│   ├── ruoyi-common-ratelimiter/   # 限流控制
│   ├── ruoyi-common-redis/         # Redis 配置
│   ├── ruoyi-common-satoken/       # Sa-Token 认证
│   ├── ruoyi-common-security/      # 安全配置
│   ├── ruoyi-common-sensitive/     # 数据脱敏
│   ├── ruoyi-common-sms/           # 短信发送
│   ├── ruoyi-common-social/        # 社交登录
│   ├── ruoyi-common-sse/           # SSE 推送
│   ├── ruoyi-common-tenant/        # 多租户支持
│   ├── ruoyi-common-translation/   # 数据翻译
│   ├── ruoyi-common-web/           # Web 配置
│   └── ruoyi-common-websocket/     # WebSocket 支持
│
├── ruoyi-extend/                   # 扩展模块
│   ├── ruoyi-monitor-admin/        # 监控管理后台
│   └── ruoyi-snailjob-server/      # 分布式任务调度中心
│
├── ruoyi-modules/                  # 业务模块
│   ├── ruoyi-demo/                 # 示例模块
│   ├── ruoyi-generator/            # 代码生成器
│   ├── ruoyi-job/                  # 定时任务模块
│   ├── ruoyi-system/               # 系统管理模块
│   └── ruoyi-workflow/             # 工作流模块
│
├── script/                         # 脚本目录
│   ├── bin/                        # 启动脚本
│   ├── docker/                     # Docker 配置
│   ├── leave/                      # 请假示例
│   └── sql/                        # SQL 脚本
│
├── specs/                   # 规范文档目录 (本目录)
│
├── pom.xml                         # Maven 父工程配置
├── README.md                       # 项目说明
└── LICENSE                         # 开源协议
```

---

## 三、模块依赖关系

### 3.1 依赖层次

```
                        ┌─────────────────┐
                        │   ruoyi-admin   │
                        │   (主启动模块)   │
                        └────────┬────────┘
                                 │
            ┌────────────────────┼────────────────────┐
            │                    │                    │
            ▼                    ▼                    ▼
    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
    │ ruoyi-common │    │ruoyi-modules │    │ ruoyi-extend │
    │  (通用模块)   │    │ (业务模块)    │    │  (扩展模块)   │
    └──────────────┘    └──────────────┘    └──────────────┘
```

### 3.2 通用模块依赖

```
ruoyi-common-core (核心基础)
    ├── ruoyi-common-json
    ├── ruoyi-common-log
    └── ruoyi-common-security

ruoyi-common-web (Web 层依赖)
    ├── ruoyi-common-core
    ├── ruoyi-common-doc
    └── ruoyi-common-satoken

ruoyi-common-mybatis (数据层依赖)
    ├── ruoyi-common-core
    ├── ruoyi-common-tenant
    └── ruoyi-common-translation
```

### 3.3 业务模块依赖

| 模块 | 依赖 |
|------|------|
| ruoyi-system | ruoyi-common-web, ruoyi-common-mybatis |
| ruoyi-generator | ruoyi-common-web, ruoyi-common-mybatis |
| ruoyi-job | ruoyi-common-job, ruoyi-common-web |
| ruoyi-demo | ruoyi-common-web, ruoyi-common-mybatis |
| ruoyi-workflow | ruoyi-common-web, ruoyi-common-mybatis |

---

## 四、分层架构

### 4.1 标准分层

```
┌────────────────────────────────────────────────────────────┐
│                    Controller 层                            │
│  位置：ruoyi-admin/src/main/java/.../web/controller/       │
│  职责：接收 HTTP 请求、参数校验、调用 Service、返回响应      │
└────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────┐
│                     Service 层                              │
│  位置：ruoyi-modules/ruoyi-system/src/main/java/.../service/ │
│  职责：业务逻辑实现、事务控制、调用 Mapper                   │
└────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────┐
│                     Mapper 层                               │
│  位置：ruoyi-modules/ruoyi-system/src/main/java/.../mapper/  │
│  职责：数据库访问接口、SQL 执行                              │
└────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────┐
│                     Entity 层                               │
│  位置：ruoyi-modules/ruoyi-system/src/main/java/.../domain/  │
│  职责：数据实体定义、数据库表映射                            │
└────────────────────────────────────────────────────────────┘
```

### 4.2 包路径规范

| 层级 | 包路径前缀 | 示例 |
|------|-----------|------|
| Controller | `org.dromara.ruoyi.web.controller` | `.../system/SysUserController.java` |
| Service | `org.dromara.ruoyi.system.service` | `ISysUserService.java` |
| Service Impl | `org.dromara.ruoyi.system.service.impl` | `SysUserServiceImpl.java` |
| Mapper | `org.dromara.ruoyi.system.mapper` | `SysUserMapper.java` |
| Entity | `org.dromara.ruoyi.system.domain` | `SysUser.java` |
| DTO | `org.dromara.ruoyi.system.domain.dto` | `SysUserDTO.java` |
| VO | `org.dromara.ruoyi.system.domain.vo` | `SysUserVO.java` |

---

## 五、关键目录说明

### 5.1 资源文件目录

```
ruoyi-admin/src/main/resources/
├── application.yml           # 主配置文件
├── application-*.yml         # 环境配置文件
├── logback.xml               # 日志配置
├── banner.txt                # 启动 Banner
└── i18n/                     # 国际化资源
    └── messages.properties
```

### 5.2 前端资源

```
ruoyi-admin/src/main/resources/assets/
├── css/                      # 样式文件
├── js/                       # JavaScript 文件
└── images/                   # 图片资源
```

### 5.3 SQL 脚本

```
script/sql/
├── mysql/                    # MySQL 脚本
│   ├── ry_202*.sql          # 版本脚本
│   └── quartz.sql           # 定时任务表
├── oracle/                   # Oracle 脚本
└── postgresql/               # PostgreSQL 脚本
```

---

## 六、与 RuoYi-Vue 结构对比

### 6.1 模块变化

| RuoYi-Vue 模块 | RuoYi-Vue-Plus 模块 | 说明 |
|---------------|---------------------|------|
| ruoyi-framework | ruoyi-common-* | 框架功能拆分为多个 common 模块 |
| ruoyi-system | ruoyi-modules/ruoyi-system | 移至 modules 目录 |
| ruoyi-quartz | ruoyi-modules/ruoyi-job | 升级为 SnailJob |
| ruoyi-generator | ruoyi-modules/ruoyi-generator | 功能增强 |
| - | ruoyi-modules/ruoyi-workflow | 新增工作流模块 |
| - | ruoyi-modules/ruoyi-demo | 新增示例模块 |
| - | ruoyi-extend/* | 新增扩展模块 |

### 6.2 包名变化

| RuoYi-Vue | RuoYi-Vue-Plus |
|-----------|----------------|
| `com.ruoyi.*` | `org.dromara.ruoyi.*` |
| `com.ruoyi.common.*` | `org.dromara.common.*` |
| `com.ruoyi.system.*` | `org.dromara.ruoyi.system.*` |

---

## 七、规范文档目录结构 (specs)

```
specs/
├── SPECS_CHECKLIST.md        # 检查清单
├── STRUCTURE.md              # 本文件 (项目结构)
├── README.md                 # 文档索引 (待创建)
├── API.md                    # API 清单 (待创建)
├── TECH.md                   # 技术选型 (待创建)
├── ARCHITECTURE.md           # 整体架构 (待创建)
├── constitution.md           # 宪法原则 (待创建)
├── overall-spec.md           # 整体规格 (待创建)
├── overall-plan.md           # 整体方案 (待创建)
├── overall-data-model.md     # 整体数据模型 (待创建)
├── overall-api.md            # 整体接口模型 (待创建)
│
├── 001-tenant/               # 租户管理 (待创建)
├── 002-user/                 # 用户管理 (待创建)
├── 003-role/                 # 角色管理 (待创建)
├── 004-dept/                 # 部门管理 (待创建)
├── 005-post/                 # 岗位管理 (待创建)
├── 006-menu/                 # 菜单管理 (待创建)
├── 007-dict/                 # 字典管理 (待创建)
├── 008-config/               # 参数配置 (待创建)
├── 009-notice/               # 通知公告 (待创建)
├── 010-oper-log/             # 操作日志 (待创建)
├── 011-login-log/            # 登录日志 (待创建)
│
├── 012-online/               # 在线用户 (待创建)
├── 013-job/                  # 定时任务 (待创建)
├── 014-cache/                # 缓存管理 (待创建)
├── 015-server/               # 服务监控 (待创建)
├── 016-pool/                 # 连接池 (待创建)
│
├── 017-gen/                  # 代码生成 (待创建)
├── 018-swagger/              # API 文档 (待创建)
├── 019-build/                # 构建工具 (待创建)
│
└── feature-1XX/              # 特性模块
    ├── 101-workflow/         # 工作流 (待创建)
    ├── 102-demo/             # 示例模块 (待创建)
    ├── 103-oss/              # 对象存储 (待创建)
    ├── 104-encrypt/          # 加密 (待创建)
    ├── 105-sensitive/        # 数据脱敏 (待创建)
    ├── 106-idempotent/       # 幂等 (待创建)
    ├── 107-ratelimiter/      # 限流 (待创建)
    ├── 108-sms/              # SMS (待创建)
    ├── 109-social/           # 社交登录 (待创建)
    ├── 110-mail/             # 邮件 (待创建)
    ├── 111-distributed-job/  # 分布式任务 (待创建)
    ├── 112-websocket/        # WebSocket (待创建)
    └── 113-sse/              # SSE (待创建)
```

---

**文档版本：** 1.0  
**生成日期：** 2026-03-13  
**维护者：** 开发团队
