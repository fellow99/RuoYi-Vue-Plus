# 缓存监控功能规格 (spec.md)

> 模块：014-cache | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
缓存监控模块提供 Redis 缓存服务器的实时状态查看功能，包括服务器基本信息、内存使用、客户端连接数、键数量统计以及命令执行统计。

### 1.2 解决的问题
- 运维人员需要了解 Redis 服务器的运行状态和健康状况
- 需要监控 Redis 内存使用情况，及时发现内存压力
- 需要了解命令执行频率分布，分析热点操作
- 需要在系统管理界面直观展示缓存信息，无需登录 Redis 服务器

### 1.3 范围
- ✅ Redis 服务器核心信息查询（info）
- ✅ 数据库键数量统计（dbSize）
- ✅ 命令执行统计（commandstats）
- ✅ 权限控制访问（仅管理员可查看）
- ❌ Redis 键值浏览与编辑（由 RedisUtils 工具类提供支持）
- ❌ 缓存命中率统计与告警（由 Spring Cache 层处理）

## 2. 用户故事
- 作为**运维管理员**，我可以查看 Redis 服务器的运行信息（版本、内存、连接数等），以便评估服务器健康状况
- 作为**运维管理员**，我可以查看当前数据库中的键总数，以便了解缓存使用规模
- 作为**运维管理员**，我可以查看各个 Redis 命令的执行次数统计，以便分析系统中哪些操作最频繁
- 作为**安全管理员**，我可以确保只有具备 `monitor:cache:list` 权限的用户才能访问缓存监控

## 3. 功能需求

- FR-014-001: 系统 MUST 通过 `GET /monitor/cache` 接口返回 Redis 缓存监控信息
- FR-014-002: 系统 MUST 返回 Redis INFO 命令的全部属性信息（包括 server、clients、memory、stats、CPU、keyspace 等段）
- FR-014-003: 系统 MUST 返回当前数据库的键数量（dbSize）
- FR-014-004: 系统 MUST 返回命令执行统计列表（commandstats），每条记录包含命令名称和调用次数
- FR-014-005: 系统 MUST 基于 `monitor:cache:list` 权限进行访问控制
- FR-014-006: 系统 MUST 在获取监控数据后正确释放 Redis 连接，防止连接泄漏
- FR-014-007: 接口 MUST 统一返回 `R<CacheListInfoVo>` 响应格式

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| CacheListInfoVo | 缓存信息响应体（内嵌 record） | info（Properties）, dbSize（Long）, commandStats（List<Map>） |
| Properties | Redis INFO 返回的全部属性 | 包含 server、clients、memory、persistence、stats、replication、CPU、keyspace 等 |
| RedisConnection | Spring Data Redis 连接 | 底层通过 RedissonConnectionFactory 获取，由 Redisson Netty 客户端实现 |

## 5. 验收场景

### 场景：管理员查看缓存监控
- Given 管理员已登录系统且拥有 `monitor:cache:list` 权限
- When 访问缓存监控页面 → 系统发起 `GET /monitor/cache` 请求
- Then 返回 Redis 的 INFO 全量属性、数据库键数量、命令统计列表

### 场景：无权限用户访问被拒绝
- Given 普通用户没有 `monitor:cache:list` 权限
- When 尝试访问 `GET /monitor/cache`
- Then 系统返回权限不足错误，拒绝访问

### 场景：Redis 连接异常
- Given Redis 服务不可用
- When 访问缓存监控页面
- Then 系统返回错误信息，提示 Redis 连接失败

## 6. 非功能需求
- Redis 连接 MUST 在 finally 块中通过 `RedisConnectionUtils.releaseConnection()` 释放，确保归还连接池
- 响应数据 MUST 实时获取（每次请求直接从 Redis 读取，不做缓存）
- 接口响应时间 SHOULD 小于 2 秒（正常网络条件下）

## 7. 依赖
- Redisson（`redisson-spring-boot-starter`）— 提供 RedissonClient 和 RedissonConnectionFactory
- Spring Data Redis — 提供 RedisConnection 抽象层
- RedisConfig（`ruoyi-common-redis`）— Redisson 客户端配置（编解码器、连接模式、键前缀）
- Sa-Token 权限框架 — 提供 `@SaCheckPermission` 注解控制
