# 在线用户功能规格 (spec.md)

> 模块：012-online | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
在线用户模块提供当前已登录系统的所有在线会话的监控视图，支持管理员按 IP 或用户名筛选在线用户、强制踢出指定会话，以及当前登录用户查看和管理自己的多设备登录会话。

### 1.2 解决的问题
- 管理员需要实时了解系统当前有多少在线用户、从哪里登录、使用什么设备
- 管理员需要能够强制踢出异常会话（如被盗号、违规操作）
- 用户需要查看自己当前在哪些设备上登录，并能够下线不再使用的设备

### 1.3 范围
- ✅ 查看所有在线用户列表（管理员视图），支持按 IP、用户名筛选
- ✅ 管理员强制踢出指定 token 会话（`forceLogout`）
- ✅ 当前用户查看自己的在线设备列表（`getInfo`）
- ✅ 当前用户踢出自己的指定设备（`remove`）
- ❌ 不涉及登录认证流程本身
- ❌ 不存储到数据库，数据完全基于 Redis

## 2. 用户故事
- 作为**管理员**，我可以看到所有当前在线用户及其 IP、浏览器、登录时间，以便监控系统使用情况
- 作为**管理员**，当我发现可疑会话时，可以强制将其踢出系统
- 作为**普通用户**，我可以查看自己在哪些设备上登录了系统，并下线不再使用的设备

## 3. 功能需求

- FR-012-001: 系统 MUST 提供在线用户监控列表 API（`GET /monitor/online/list`），从 Redis 中扫描所有 `online_tokens:*` 缓存 key，聚合为在线用户列表
- FR-012-002: 系统 MUST 支持按 IP 地址（`ipaddr`）和用户名（`userName`）过滤在线用户列表，支持单独过滤和组合过滤
- FR-012-003: 系统 MUST 使用虚拟线程（`ThreadUtils.virtualSubmitAll`）并行加载所有在线 token 对应的用户信息（`UserOnlineDTO`），提升大规模在线用户场景下的查询性能
- FR-012-004: 系统 MUST 自动过滤已过期的 token（`StpUtil.stpLogic.getTokenActiveTimeoutByToken(token) < -1`），确保列表只显示真正在线的用户
- FR-012-005: 系统 MUST 支持管理员通过 `DELETE /monitor/online/{tokenId}` 强制踢出指定 token 会话（调用 `StpUtil.kickoutByTokenValue(tokenId)`）
- FR-012-006: 系统 MUST 提供当前用户在线设备查询 API（`GET /monitor/online`），返回当前账号下所有仍有效的 token 会话
- FR-012-007: 系统 MUST 支持当前用户踢出自己的指定设备（`DELETE /monitor/online/myself/{tokenId}`），需校验该 token 确实属于当前用户
- FR-012-008: 系统 MUST 将在线用户列表按登录时间倒序排列（最近登录的在前）
- FR-012-009: 系统 MUST 使用 `@RepeatSubmit` 防重复提交保护踢出操作，防止重复踢出同一用户

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| SysUserOnline | 在线会话视图对象（非数据库表，纯 VO） | tokenId, userName, deptName, clientKey, deviceType, ipaddr, loginLocation, browser, os, loginTime |
| UserOnlineDTO | Redis 中缓存的在线用户 DTO（ruoyi-api） | tokenId, userName, deptName, clientKey, deviceType, ipaddr, loginLocation, browser, os, loginTime |
| Redis `online_tokens:{token}` | 在线用户缓存数据 | 存储 `UserOnlineDTO` JSON，由 Sa-Token 登录流程写入，登出时删除 |

## 5. 验收场景

### 场景：查看在线用户列表
- Given 系统中有多个用户在线
- When 管理员打开在线用户监控页面
- Then 系统展示所有在线用户列表，包含用户名、部门、IP、浏览器、登录时间等信息

### 场景：按IP和用户名筛选
- Given 管理员输入 IP "192.168.1.100" 和用户名 "admin"
- When 点击查询
- Then 系统只返回同时匹配该 IP 和用户名的在线用户

### 场景：强制踢出用户
- Given 管理员发现某用户存在异常行为
- When 点击"强退"按钮
- Then 该用户的 token 被 Sa-Token 标记为踢出，用户下次请求时将被拒绝访问

### 场景：用户查看自己的在线设备
- Given 用户"张三"在 Chrome 和 Firefox 上同时登录
- When 用户进入个人中心的"在线设备"页面
- Then 系统列出两个设备会话，用户可以选择下线 Firefox 上的会话

### 场景：用户踢出自己的设备
- Given 用户"张三"有两个在线设备
- When 用户点击 Firefox 设备的"下线"按钮
- Then Firefox 上的 token 被踢出，Chrome 上的会话不受影响

## 6. 非功能需求
- 在线用户列表数据 MUST 不经过数据库，完全基于 Redis 缓存
- 在大规模在线用户场景下（如数千并发），使用虚拟线程并行加载 token 信息以控制查询耗时
- 踢出操作 MUST 捕获 `NotLoginException`，避免已离线的 token 踢出时报错
- 在线用户缓存数据在 Sa-Token 登录时写入（`StpUtil.login()` 后的监听器中），登出时由 Sa-Token 自动清理
- `@RepeatSubmit` 防止管理员短时间内重复执行踢出操作

## 7. 依赖
- Sa-Token（`StpUtil` 踢出、token 超时检测、当前用户 token 列表）
- ruoyi-common-redis（`RedisUtils` 扫描/读取 `online_tokens:*` 缓存）
- ruoyi-common-core（`ThreadUtils.virtualSubmitAll` 虚拟线程、`StreamUtils`、`CacheNames.ONLINE_TOKEN_KEY`）
- ruoyi-api（`UserOnlineDTO` 跨模块在线用户数据传输对象）
- ruoyi-common-security（Sa-Token `@SaCheckPermission` 权限控制）
- ruoyi-common-log（`@Log` 注解记录强退操作）
