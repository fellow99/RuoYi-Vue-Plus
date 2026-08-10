# RuoYi-Vue-Plus 项目结构文档 (STRUCTURE.md)

**版本：** 6.0.0  
**生成时间：** 2026-08-10  
**项目：** RuoYi-Vue-Plus

---

## 一、项目概述

RuoYi-Vue-Plus 是基于 RuoYi-Vue 全方位重写的分布式集群快速开发平台，针对分布式集群场景进行全面升级。v6.0.0 基于 **Spring Boot 4.1 + JDK 21**，相比 v5 移除了多租户功能，新增 AI、LiteFlow、MQTT、MCP、消息推送、Elasticsearch 等模块。

### 1.1 核心差异（v6 vs RuoYi-Vue）

| 特性 | RuoYi-Vue | RuoYi-Vue-Plus v6 |
|------|-----------|-------------------|
| Spring Boot | 2.x | 4.1.0 |
| JDK | 8/11 | 21/25 |
| 权限框架 | Spring Security | Sa-Token 1.45.0 |
| ORM | MyBatis (XML) | MyBatis-Plus 3.5.17 |
| Redis 客户端 | Lettuce + RedisTemplate | Redisson 4.6.1 |
| 序列化 | FastJSON | Jackson |
| Web 容器 | Tomcat | Jetty (Netty) |
| 分布式锁 | ❌ | ✅ (Lock4j 2.2.7) |
| 分布式任务 | Quartz | SnailJob 2.0.2 |
| 文件存储 | 本地文件 | MinIO + RustFS + S3 |
| 工作流 | ❌ | ✅ (Warm-Flow 1.8.9) |
| AI 集成 | ❌ | ✅ (Spring AI 2.0.0 + SnailAI) |
| 数据脱敏 | ❌ | ✅ (注解 + 序列化脱敏) |
| 数据加解密 | ❌ | ✅ (注解 + 拦截器) |
| 接口加密 | ❌ | ✅ (动态 AES + RSA) |
| 数据翻译 | ❌ | ✅ (注解 + 动态翻译) |
| MCP 协议 | ❌ | ✅ (Spring AI MCP Server) |

---

## 二、顶层目录结构

```
RuoYi-Vue-Plus/
├── ruoyi-admin/                    # 主启动模块（应用入口）
│   ├── Dockerfile                  # Docker 镜像构建
│   ├── pom.xml                     # 模块 POM
│   └── src/
│       ├── main/java/org/dromara/
│       │   ├── RuoYiApplication.java   # Spring Boot 启动类
│       │   └── web/controller/         # 全局 Controller（认证/验证码/首页）
│       └── main/resources/
│           ├── application.yml         # 主配置文件
│           ├── application-dev.yml     # 开发环境配置
│           ├── application-prod.yml    # 生产环境配置
│           └── logback-plus.xml        # 日志配置
│
├── ruoyi-api/                      # API 接口模块（对外接口定义）
│   ├── pom.xml
│   └── src/main/java/org/dromara/
│
├── ruoyi-common/                   # 通用模块集合（25 个子模块）
│   ├── pom.xml
│   ├── ruoyi-common-bom/           # 依赖版本管理
│   ├── ruoyi-common-core/          # 核心工具类、异常、校验、配置
│   ├── ruoyi-common-web/           # Web 配置（CORS/XSS/验证码）
│   ├── ruoyi-common-security/      # 安全配置（URL 收集/安全属性）
│   ├── ruoyi-common-satoken/       # Sa-Token 认证集成
│   ├── ruoyi-common-mybatis/       # MyBatis-Plus 集成（数据权限/SQL 日志/分页）
│   ├── ruoyi-common-redis/         # Redis/Redisson 集成
│   ├── ruoyi-common-doc/           # SpringDoc 接口文档配置
│   ├── ruoyi-common-json/          # JSON 序列化配置
│   ├── ruoyi-common-log/           # 操作日志记录（AOP）
│   ├── ruoyi-common-encrypt/       # 数据加解密（API 传输/数据库字段）
│   ├── ruoyi-common-sensitive/     # 数据脱敏（注解 + 序列化）
│   ├── ruoyi-common-translation/   # 数据翻译（注解 + 序列化）
│   ├── ruoyi-common-excel/         # Excel 导入导出（Apache Fesod）
│   ├── ruoyi-common-job/           # SnailJob 任务调度客户端
│   ├── ruoyi-common-oss/           # 对象存储（MinIO/S3/RustFS）
│   ├── ruoyi-common-sms/           # 短信集成（SMS4J）
│   ├── ruoyi-common-social/        # 社交登录（JustAuth）
│   ├── ruoyi-common-mail/          # 邮件发送
│   ├── ruoyi-common-push/          # 消息推送（SSE 默认）
│   ├── ruoyi-common-ai/            # AI 集成（Spring AI）
│   ├── ruoyi-common-liteflow/      # LiteFlow 规则引擎
│   ├── ruoyi-common-mqtt/          # MQTT 消息协议
│   ├── ruoyi-common-mcp/           # MCP 协议集成
│   └── ruoyi-common-elasticsearch/ # Elasticsearch 集成
│
├── ruoyi-extend/                   # 扩展模块（独立部署服务）
│   ├── pom.xml
│   ├── ruoyi-monitor-admin/        # Spring Boot Admin 监控服务（端口 9090）
│   ├── ruoyi-snailjob-server/      # SnailJob 调度中心（端口 8800/17888）
│   └── ruoyi-snailai-server/       # SnailAI AI 服务（端口 8900/18888）
│
├── ruoyi-modules/                  # 业务模块
│   ├── pom.xml
│   ├── ruoyi-system/               # 系统管理模块（用户/角色/部门/菜单等核心 CRUD）
│   ├── ruoyi-job/                  # 定时任务业务模块
│   ├── ruoyi-gen/                  # 代码生成器模块
│   ├── ruoyi-demo/                 # 示例/Demo 模块
│   ├── ruoyi-workflow/             # 工作流模块（Warm-Flow）
│   └── ruoyi-ai/                   # AI 业务模块（SnailAI 对接）
│
├── script/                         # 脚本目录
│   ├── bin/                        # 启动脚本（ry.sh / ry.bat）
│   ├── docker/                     # Docker 编排（docker-compose / nginx / redis）
│   ├── leave/                      # 工作流请假示例数据
│   └── sql/                        # 数据库脚本（MySQL / Oracle / Postgres / SQLServer）
│
├── specs/                          # 规范文档目录（本目录）
├── .run/                           # IntelliJ IDEA Docker 运行配置
├── pom.xml                         # Maven 父工程 POM（版本管理）
├── README.md                       # 项目说明
├── LICENSE                         # MIT 开源协议
└── mvnw / mvnw.cmd                 # Maven Wrapper
```

---

## 三、模块依赖关系

### 3.1 依赖层次

```
                    ┌─────────────────┐
                    │   ruoyi-admin   │  ← 启动入口，依赖所有模块
                    └────────┬────────┘
                             │
          ┌──────────────────┼──────────────────┐
          ▼                  ▼                  ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│  ruoyi-modules  │ │  ruoyi-extend   │ │    ruoyi-api    │
│  (业务模块)      │ │  (扩展服务)      │ │  (API 接口)      │
└────────┬────────┘ └────────┬────────┘ └────────┬────────┘
         │                   │                   │
         └───────────────────┼───────────────────┘
                             ▼
                  ┌─────────────────────┐
                  │    ruoyi-common     │  ← 公共基础层（25 个子模块）
                  └─────────────────────┘
```

### 3.2 业务模块源码结构（ruoyi-system）

```
ruoyi-modules/ruoyi-system/src/main/java/org/dromara/system/
├── controller/
│   ├── system/              # 系统管理 Controller
│   │   ├── SysUserController.java       # 用户管理
│   │   ├── SysRoleController.java       # 角色管理
│   │   ├── SysDeptController.java       # 部门管理
│   │   ├── SysPostController.java       # 岗位管理
│   │   ├── SysMenuController.java       # 菜单管理
│   │   ├── SysDictTypeController.java   # 字典类型管理
│   │   ├── SysDictDataController.java   # 字典数据管理
│   │   ├── SysConfigController.java     # 参数配置
│   │   ├── SysNoticeController.java     # 通知公告
│   │   ├── SysClientController.java     # 客户端管理
│   │   ├── SysOssController.java        # 文件管理
│   │   ├── SysOssConfigController.java  # 文件配置管理
│   │   ├── SysSocialController.java     # 社交登录配置
│   │   ├── SysMessageController.java    # 消息管理
│   │   └── SysProfileController.java    # 个人中心
│   └── monitor/             # 系统监控 Controller
│       ├── SysOperlogController.java    # 操作日志
│       ├── SysLoginInfoController.java  # 登录日志
│       ├── SysUserOnlineController.java # 在线用户
│       └── CacheController.java         # 缓存监控
├── domain/                  # 领域实体
│   ├── SysUser.java / SysUserVo.java / SysUserBo.java / SysUserBody.java
│   ├── SysRole.java / SysRoleVo.java / SysRoleBo.java
│   ├── SysDept.java / SysDeptVo.java
│   ├── SysPost.java / SysPostVo.java
│   ├── SysMenu.java / SysMenuVo.java / SysMenuBo.java
│   ├── SysDictType.java / SysDictData.java / *.vo
│   ├── SysConfig.java
│   ├── SysNotice.java / SysNoticeVo.java
│   ├── SysClient.java / SysClientVo.java
│   ├── SysOss.java / SysOssConfig.java / *.vo
│   ├── SysOperLog.java / SysLogininfor.java / SysUserOnline.java
│   ├── SysSocial.java
│   └── SysMessage*.java / SysMessageConfig.java
├── mapper/                  # 数据访问层（MyBatis-Plus Mapper 接口）
│   ├── SysUserMapper.java / SysRoleMapper.java / SysDeptMapper.java ...
│   └── xml/                 # MyBatis XML 映射文件
└── service/                 # 业务逻辑层
    ├── ISysUserService.java / impl/SysUserServiceImpl.java
    ├── ISysRoleService.java / impl/SysRoleServiceImpl.java
    └── ...(对应每个 Entity 的 Service 接口与实现)
```

---

## 四、部署服务清单

| 服务 | 端口 | 说明 |
|------|------|------|
| ruoyi-admin | 8080 | 主应用服务（Jetty + Spring Boot） |
| ruoyi-monitor-admin | 9090 | Spring Boot Admin 监控服务 |
| ruoyi-snailjob-server | 8800/17888 | SnailJob 分布式任务调度中心 |
| ruoyi-snailai-server | 8900/18888 | SnailAI AI 服务 |

---

## 五、配置文件索引

| 文件 | 路径 | 说明 |
|------|------|------|
| 主配置 | ruoyi-admin/src/main/resources/application.yml | 全局配置：Jetty、Sa-Token、MyBatis-Plus、SpringDoc、加密、限流 |
| 开发环境 | ruoyi-admin/src/main/resources/application-dev.yml | MySQL 本地库、Redis 本地、SQL 日志开启 |
| 生产环境 | ruoyi-admin/src/main/resources/application-prod.yml | 连接池优化、SQL 日志关闭、多路径配置 |
| 日志配置 | ruoyi-admin/src/main/resources/logback-plus.xml | Logback 日志格式与级别 |
| 监控配置 | ruoyi-extend/ruoyi-monitor-admin/src/main/resources/application.yml | Admin Server 端口 9090 |
| Docker 编排 | script/docker/docker-compose.yml | 全栈容器编排 |
| Nginx 配置 | script/docker/nginx/conf/nginx.conf | 反向代理与静态资源 |
| Redis 配置 | script/docker/redis/conf/redis.conf | Redis 持久化配置 |

---

## 六、数据库脚本索引

| 脚本 | 路径 | 说明 |
|------|------|------|
| 主数据库 | script/sql/ry_vue.sql | 系统核心表（用户/角色/菜单/部门等） |
| 任务调度 | script/sql/ry_job.sql | SnailJob 调度表 |
| AI 模块 | script/sql/ry_ai.sql | SnailAI 相关表 |
| 工作流 | script/sql/ry_workflow.sql | Warm-Flow 工作流表 |
| Oracle | script/sql/oracle/ | Oracle 适配脚本 |
| PostgreSQL | script/sql/postgres/ | PostgreSQL 适配脚本 |
| SQLServer | script/sql/sqlserver/ | SQLServer 适配脚本 |
