# 登录日志功能规格 (spec.md)

> 模块：011-login-log | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
登录日志模块记录系统中每一次用户登录、登出、注册行为，包括登录账号、IP地址、归属地、浏览器、操作系统、登录状态（成功/失败）、失败原因等信息。同时支持管理员对登录失败次数过多的账户进行解锁操作。

### 1.2 解决的问题
- 需要追踪所有用户的登录行为，用于安全审计和异常登录检测
- 需要在用户连续登录失败达到阈值后锁定账户，并通过登录日志记录锁定事件
- 管理员需要能够解锁被锁定的账户，删除 Redis 中的错误计数 key

### 1.3 范围
- ✅ 登录/登出/注册事件自动记录（通过 `LoginInfoEvent` 异步存储）
- ✅ 登录日志分页查询，支持按 IP、用户名、状态、时间范围筛选
- ✅ 登录日志导出为 Excel
- ✅ 批量删除指定登录日志
- ✅ 清空全部登录日志（分布式锁保护）
- ✅ 管理员解锁被锁定的账户（清除 Redis `pwd_err_cnt:{username}` 缓存）
- ❌ 不处理登录认证逻辑本身（由 `SysLoginService.checkLogin()` 处理）

## 2. 用户故事
- 作为**安全管理员**，我可以查看所有用户的登录记录，以便发现异常登录行为（如异地IP、频繁失败）
- 作为**管理员**，我可以按用户名、IP 筛选登录日志，以便定位特定用户的登录历史
- 作为**管理员**，我可以导出登录日志为 Excel，以便离线分析或提交安全审计报告
- 作为**管理员**，当用户因密码错误次数过多被锁定时，我可以手动为其解锁

## 3. 功能需求

- FR-011-001: 系统 MUST 在用户登录、登出、注册时通过 `SysLoginService.recordLoginInfo()` / `SysRegisterService` 发布 `LoginInfoEvent` 事件
- FR-011-002: 系统 MUST 通过 `@Async` + `@EventListener` 异步消费 `LoginInfoEvent`，解析 User-Agent 获取浏览器和操作系统信息，调用 `AddressUtils.getRealAddressByIP()` 获取 IP 归属地，然后持久化到 `sys_login_info` 表
- FR-011-003: 系统 MUST 支持分页查询登录日志列表，支持按 IP、状态、用户名、时间区间筛选
- FR-011-004: 系统 MUST 支持导出登录日志为 Excel（使用 Apache Fesod），导出字段包含：序号、用户账号、客户端、设备类型、登录状态、登录地址、登录地点、浏览器、操作系统、提示消息、访问时间
- FR-011-005: 系统 MUST 支持批量删除指定登录日志（按 infoIds 数组）
- FR-011-006: 系统 MUST 支持清空全部登录日志，使用 `@Lock4j` 分布式锁防止并发清空
- FR-011-007: 系统 MUST 支持管理员解锁指定账户：`GET /monitor/loginInfo/unlock/{userName}` → 删除 Redis key `pwd_err_cnt:{userName}`
- FR-011-008: 系统 MUST 在用户连续密码错误达到 `maxRetryCount`（默认 5 次）后锁定账号 `lockTime`（默认 10 分钟），并在登录日志中记录失败事件

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| SysLoginInfo | 登录日志表 (sys_login_info) | infoId, userName, clientKey, deviceType, status, ipaddr, loginLocation, browser, os, msg, loginTime |
| LoginInfoEvent | 登录事件 (Spring Event) | username, status, message, ip, userAgent, clientId, args |
| Redis `pwd_err_cnt:{userName}` | 密码错误计数器 | 值为错误次数，过期时间 = lockTime（默认 10 分钟） |

## 5. 验收场景

### 场景：成功登录记录
- Given 用户输入正确的用户名和密码
- When 点击登录
- Then `sys_login_info` 表新增一条 status=0（成功）的记录，包含用户名、IP、浏览器、操作系统、归属地

### 场景：登录失败记录
- Given 用户输入错误密码
- When 点击登录
- Then `sys_login_info` 表新增一条 status=1（失败）的记录，msg 字段包含"密码错误"提示；Redis 中 `pwd_err_cnt:{userName}` 计数 +1

### 场景：账户被锁定
- Given 用户已连续 5 次密码错误
- When 第 6 次尝试登录
- Then 系统拒绝登录，提示"密码错误次数超过限制，请 10 分钟后再试"；`sys_login_info` 表新增一条失败记录

### 场景：管理员解锁账户
- Given 用户账户被锁定（Redis 中存在 `pwd_err_cnt:zhangsan`）
- When 管理员点击解锁
- Then Redis 中 `pwd_err_cnt:zhangsan` 被删除，用户可以重新尝试登录

### 场景：清空登录日志
- Given 管理员拥有 `monitor:logininfo:remove` 权限
- When 点击清空登录日志
- Then `sys_login_info` 表所有记录被删除

## 6. 非功能需求
- 登录日志记录 MUST 使用 `@Async` 异步执行，不得阻塞登录/登出/注册主流程
- User-Agent 解析使用 Hutool 的 `UserAgentUtil.parse()` 获取浏览器和 OS 名称
- IP 归属地查询使用 `AddressUtils.getRealAddressByIP()`（可能涉及远程 API 调用）
- Excel 导出使用 Apache Fesod，状态字段通过 `@ExcelDictFormat(dictType = "sys_common_status")` 字典翻译

## 7. 依赖
- ruoyi-admin（`SysLoginService` 发布 `LoginInfoEvent`；`SysRegisterService` 发布注册事件）
- ruoyi-common-log（`LoginInfoEvent` 事件定义）
- ruoyi-common-core（`AddressUtils` IP 定位、`ServletUtils` 获取客户端信息）
- ruoyi-common-excel（Apache Fesod Excel 导出）
- ruoyi-common-security（Sa-Token `@SaCheckPermission` 权限控制）
- ruoyi-common-redis（`@Lock4j` 分布式锁、`RedisUtils` 操作错误计数缓存、`@RepeatSubmit` 防重复提交）
- ruoyi-system（`ISysClientService` 查询客户端信息）
