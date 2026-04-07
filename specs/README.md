# RuoYi-Vue-Plus 规范文档 (specs-plan-e)

> 完整的系统规范文档，涵盖架构设计、功能规格、API 接口、数据模型和技术方案

**版本：** 5.5.3  
**生成时间：** 2026-03-13  
**文档状态：** 🔄 进行中

---

## 📖 项目介绍

RuoYi-Vue-Plus 是针对 RuoYi-Vue 进行全方位重写的多租户快速开发框架，采用插件化架构设计，支持分布式集群与多租户场景。

### 技术栈

| 层级 | 技术 |
|------|------|
| **后端** | Spring Boot 3.5.9, Sa-Token, MyBatis-Plus, Redisson, MySQL |
| **前端** | Vue 3, TypeScript, Element Plus, Pinia, Axios |
| **中间件** | MinIO, SnailJob, Warm-Flow, Nginx |
| **工具** | Hutool, Lombok, SpringDoc, MapStruct-Plus |

### 核心功能

- **系统管理** - 用户、部门、岗位、菜单、角色、字典、参数、通知
- **系统监控** - 在线用户、定时任务、操作日志、登录日志、缓存监控、服务器监控
- **系统工具** - 代码生成、系统接口
- **租户管理** - 租户管理、租户套餐、客户端管理（新增）
- **增强功能** - 文件管理、数据脱敏、数据加密、分布式锁、WebSocket、工作流等（新增）

### 与 RuoYi-Vue 的主要差异

| 特性 | RuoYi-Vue | RuoYi-Vue-Plus |
|------|-----------|----------------|
| Spring Boot | 2.x | 3.5.9 |
| JDK | 8/11 | 17/21 |
| 权限框架 | Spring Security | Sa-Token |
| ORM | MyBatis (XML) | MyBatis-Plus (注解) |
| Redis 客户端 | Lettuce + RedisTemplate | Redisson |
| 多租户 | ❌ | ✅ |
| 数据脱敏 | ❌ | ✅ |
| 数据加密 | ❌ | ✅ |
| 分布式锁 | ❌ | ✅ |
| 分布式任务 | Quartz | SnailJob |
| 工作流 | ❌ | ✅ (Warm-Flow) |

---

## 📚 文档介绍

本规范文档采用 Speckit 工具集方法论从现有代码逆向生成，遵循以下规范：

### 文档类型

| 文档类型 | 用途 | 内容 |
|----------|------|------|
| `spec.md` | 功能规格 | 用户故事、功能需求、验收标准、成功标准 |
| `plan.md` | 技术方案 | 架构设计、技术选型、实现计划 |
| `task.md` | 任务清单 | 开发任务、依赖关系、优先级 |
| `user-story.md` | 用户故事 | 详细用户故事和场景 |
| `pages.md` | 前端页面 | 页面布局、组件结构、交互设计（仅前端模块） |
| `api.md` | API 接口 | 接口定义、请求/响应、错误码 |

### 文档结构

```
specs-plan-e/
├── 核心文档                      # 整体规格和架构文档
│   ├── README.md                # 本文档（文档索引）
│   ├── SPECS_CHECKLIST.md       # 完成情况清单
│   ├── STRUCTURE.md             # 项目目录结构
│   ├── TECH.md                  # 技术栈说明
│   ├── ARCHITECTURE.md          # 系统架构
│   ├── constitution.md          # 项目章程
│   ├── overall-spec.md          # 整体规格（待生成）
│   ├── overall-plan.md          # 整体技术方案（待生成）
│   ├── overall-api.md           # 整体 API 规范（待生成）
│   └── overall-data-model.md    # 整体数据模型（待生成）
├── 功能模块规范                   # 各功能模块详细规格
│   ├── 001-tenant/              # 租户管理
│   ├── 002-user/                # 用户管理
│   ├── 003-role/                # 角色管理
│   ├── 004-dept/                # 部门管理
│   ├── 005-post/                # 岗位管理
│   ├── 006-menu/                # 菜单管理
│   ├── 007-dict/                # 字典管理
│   ├── 008-config/              # 参数配置
│   ├── 009-notice/              # 通知公告
│   ├── 010-oper-log/            # 操作日志
│   ├── 011-login-log/           # 登录日志
│   └── feature-1XX/             # Plus 增强模块
└── 参考文档                      # 来自 RuoYi-Vue 的参考
```

---

## 📁 文档索引

### 核心文档

| 文档 | 说明 | 状态 | 大小 |
|------|------|------|------|
| [README.md](README.md) | 文档索引 | ✅ 已完成 | - |
| [SPECS_CHECKLIST.md](SPECS_CHECKLIST.md) | 规范检查清单 | ✅ 已完成 | 4.1KB |
| [STRUCTURE.md](STRUCTURE.md) | 项目目录结构 | ✅ 已完成 | 13.7KB |
| [TECH.md](TECH.md) | 技术栈说明 | ✅ 已完成 | 9.9KB |
| [constitution.md](constitution.md) | 项目章程与原则 | ✅ 已完成 | 6.0KB |
| [ARCHITECTURE.md](ARCHITECTURE.md) | 系统架构 | ✅ 已完成 | 18.3KB |
| [overall-spec.md](overall-spec.md) | 整体规格 | ⏳ 待生成 | - |
| [overall-plan.md](overall-plan.md) | 整体技术方案 | ⏳ 待生成 | - |
| [overall-api.md](overall-api.md) | 整体 API 规范 | ⏳ 待生成 | - |
| [overall-data-model.md](overall-data-model.md) | 整体数据模型 | ⏳ 待生成 | - |

### 功能模块规范（待生成）

#### 系统管理模块 (001-011)

| 编号 | 模块 | 文档 | 状态 |
|------|------|------|------|
| 001 | 租户管理 | spec, data-model, api, pages | ⏳ 待开始 |
| 002 | 用户管理 | spec, data-model, api, pages | ⏳ 待开始 |
| 003 | 角色管理 | spec, data-model, api, pages | ⏳ 待开始 |
| 004 | 部门管理 | spec, data-model, api, pages | ⏳ 待开始 |
| 005 | 岗位管理 | spec, data-model, api, pages | ⏳ 待开始 |
| 006 | 菜单管理 | spec, data-model, api, pages | ⏳ 待开始 |
| 007 | 字典管理 | spec, data-model, api, pages | ⏳ 待开始 |
| 008 | 参数配置 | spec, data-model, api, pages | ⏳ 待开始 |
| 009 | 通知公告 | spec, data-model, api, pages | ⏳ 待开始 |
| 010 | 操作日志 | spec, data-model, api | ⏳ 待开始 |
| 011 | 登录日志 | spec, data-model, api | ⏳ 待开始 |

#### 系统监控模块 (012-016)

| 编号 | 模块 | 文档 | 状态 |
|------|------|------|------|
| 012 | 在线用户 | spec, data-model, api, pages | ⏳ 待开始 |
| 013 | 定时任务 | spec, data-model, api, pages | ⏳ 待开始 |
| 014 | 缓存管理 | spec, data-model, api, pages | ⏳ 待开始 |
| 015 | 服务监控 | spec, data-model, api, pages | ⏳ 待开始 |
| 016 | 连接池监控 | spec, data-model, api, pages | ⏳ 待开始 |

#### 系统工具模块 (017-019)

| 编号 | 模块 | 文档 | 状态 |
|------|------|------|------|
| 017 | 代码生成 | spec, data-model, api, pages | ⏳ 待开始 |
| 018 | API 文档 | spec, api | ⏳ 待开始 |
| 019 | 构建工具 | spec, plan | ⏳ 待开始 |

#### Plus 增强模块 (feature-1XX)

| 编号 | 模块 | 文档 | 状态 |
|------|------|------|------|
| 101 | 工作流 | spec, data-model, api | ⏳ 待开始 |
| 102 | 示例模块 | spec | ⏳ 待开始 |
| 103 | 对象存储 | spec, data-model, api | ⏳ 待开始 |
| 104 | 加密模块 | spec, plan | ⏳ 待开始 |
| 105 | 数据脱敏 | spec, plan | ⏳ 待开始 |
| 106 | 幂等控制 | spec, plan | ⏳ 待开始 |
| 107 | 限流控制 | spec, plan | ⏳ 待开始 |
| 108 | 短信服务 | spec, plan | ⏳ 待开始 |
| 109 | 社交登录 | spec, plan | ⏳ 待开始 |
| 110 | 邮件服务 | spec, plan | ⏳ 待开始 |
| 111 | 分布式任务 | spec, plan | ⏳ 待开始 |
| 112 | WebSocket | spec, plan | ⏳ 待开始 |
| 113 | SSE 推送 | spec, plan | ⏳ 待开始 |

---

## 📊 进度统计

| 类别 | 总数 | 已完成 | 进行中 | 待开始 | 完成率 |
|------|------|--------|--------|--------|--------|
| 核心文档 | 10 | 6 | 0 | 4 | 60% |
| 系统管理模块 | 11 | 0 | 0 | 11 | 0% |
| 系统监控模块 | 5 | 0 | 0 | 5 | 0% |
| 系统工具模块 | 3 | 0 | 0 | 3 | 0% |
| Plus 增强模块 | 13 | 0 | 0 | 13 | 0% |
| **总计** | **42** | **6** | **0** | **36** | **14%** |

---

## 🔧 Speckit 工具流程

本工程采用 **speckit-baseline** 方法论逆向生成规范文档：

```
1. 扫描代码结构 → STRUCTURE.md
2. 分析技术栈 → TECH.md
3. 阅读 README → overall-spec.md
4. 分析模块依赖 → overall-plan.md
5. 扫描数据库 → overall-data-model.md
6. 扫描 Controller → overall-api.md
7. 细化模块 → 各模块 spec/plan/api/pages.md
```

---

## 📝 使用说明

### 阅读顺序建议

1. **新手入门：** README.md → STRUCTURE.md → TECH.md → constitution.md
2. **架构理解：** ARCHITECTURE.md → overall-plan.md → overall-data-model.md
3. **开发参考：** overall-api.md → 各模块 spec.md → 各模块 api.md
4. **前端开发：** 各模块 pages.md → overall-api.md

### 文档更新规范

- **代码变更时：** 同步更新相关 API 文档和 spec 文档
- **重大重构时：** 更新 ARCHITECTURE.md 和 overall-plan.md
- **技术栈升级时：** 更新 TECH.md 和 constitution.md
- **版本发布时：** 更新所有文档的版本号

---

## 🔗 参考资源

- [RuoYi-Vue-Plus 官方文档](https://plus-doc.dromara.org)
- [RuoYi-Vue-Plus Gitee](https://gitee.com/dromara/RuoYi-Vue-Plus)
- [RuoYi-Vue-Plus GitHub](https://github.com/dromara/RuoYi-Vue-Plus)
- [Spring Boot 官方文档](https://spring.io/projects/spring-boot)
- [MyBatis-Plus 官方文档](https://baomidou.com)
- [Sa-Token 官方文档](https://sa-token.cc)
- [Redisson 官方文档](https://redisson.org)

---

**最后更新：** 2026-03-13  
**维护者：** 开发团队
