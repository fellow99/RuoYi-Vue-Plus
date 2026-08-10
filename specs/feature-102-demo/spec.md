# 示例模块功能规格 (spec.md)

> 模块：feature-102-demo | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
提供 RuoYi-Vue-Plus 框架全部增强功能的完整使用示例，通过可运行的 Demo 控制器演示缓存、分布式锁、限流、短信、邮件、加密、脱敏、国际化、Excel 导入导出、WebSocket、MQTT、MCP、Elasticsearch 等 Plus 特性，降低开发者学习成本。

### 1.2 范围
- ✅ Redis 缓存操作示例（@Cacheable 注解用法）
- ✅ 分布式锁示例（Lock4j/Redisson）
- ✅ 分布式限流示例（RateLimiter 注解）
- ✅ Redis 发布订阅示例
- ✅ Redis 优先级队列示例
- ✅ 短信发送示例（Sms4j）
- ✅ 邮件发送示例
- ✅ 数据加密示例（ApiEncrypt + 数据库字段加密）
- ✅ 数据脱敏示例（@Sensitive 注解）
- ✅ 国际化示例（i18n 动态语言切换）
- ✅ Excel 导入导出示例
- ✅ 批量处理示例
- ✅ 树形结构示例
- ✅ 通用 CRUD 示例（MyBatis-Plus）
- ✅ Swagger/SpringDoc 接口文档示例
- ✅ Sa-Token 权限认证示例
- ✅ WebSocket 实时通信示例
- ✅ MQTT 消息协议示例
- ✅ MCP 模型上下文协议示例
- ✅ Elasticsearch 全文搜索示例

## 2. 用户故事
- 作为**开发者**，我可以运行 Demo 模块快速了解各 Plus 特性的用法
- 作为**新成员**，我可以通过 Demo 示例快速上手框架开发
- 作为**架构师**，我可以参考 Demo 代码评估框架能力范围

## 3. 功能需求
- FR-102-001: 系统 MUST 提供 20 个 Demo 控制器覆盖所有 Plus 增强功能
- FR-102-002: 所有 Demo 控制器 MUST 挂载在 /demo/* 路径下
- FR-102-003: 系统 SHOULD 提供 CRUD 和树形结构的完整 MyBatis-Plus 示例
- FR-102-004: 系统 MUST 演示 Redis 缓存、分布式锁、限流、发布订阅四种模式
- FR-102-005: 系统 SHOULD 演示短信、邮件、Excel、国际化、加密、脱敏六种工具能力
- FR-102-006: 系统 SHOULD 演示 WebSocket、MQTT、MCP、Elasticsearch 四种扩展协议
- FR-102-007: Demo 模块作为库模块，不包含独立启动类，挂载到 ruoyi-admin

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| TestDemo | 通用 CRUD 示例实体 |
| TestDemoEncrypt | 数据库字段加密示例实体 |
| TestTree | 树形结构示例实体 |
| Document | Elasticsearch 文档映射实体 |

## 5. 依赖
- ruoyi-common-redis（缓存/锁/限流/队列）
- ruoyi-common-encrypt（加解密）
- ruoyi-common-sensitive（脱敏）
- ruoyi-common-sms（短信）
- ruoyi-common-mail（邮件）
- ruoyi-common-excel（Excel）
- ruoyi-common-mqtt（MQTT）
- ruoyi-common-mcp（MCP）
- ruoyi-common-elasticsearch（ES）
- ruoyi-common-doc（SpringDoc）
- ruoyi-common-translation（数据翻译）
- ruoyi-common-security（Sa-Token）
- 挂载模块：ruoyi-admin（DromaraApplication）
