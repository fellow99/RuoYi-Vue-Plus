# 操作日志功能规格 (spec.md)

> 模块：010-oper-log | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
操作日志模块负责记录系统中所有带有 `@Log` 注解的 Controller 方法调用记录，包括请求参数、响应结果、操作人、操作时间、消耗时长等信息，供管理员事后审计和排查问题。

### 1.2 解决的问题
- 需要完整记录后台用户的每一次增删改查操作，满足审计合规要求
- 操作日志记录不能阻塞主业务流程（必须异步执行）
- 管理员需要按条件查询、导出、批量删除和清空操作日志

### 1.3 范围
- ✅ 通过 `@Log` 注解 + AOP 切面自动拦截并记录操作日志
- ✅ 操作日志分页查询，支持按操作人、业务类型、状态、时间范围等条件筛选
- ✅ 操作日志导出为 Excel
- ✅ 批量删除指定操作日志
- ✅ 清空全部操作日志（分布式锁保护）
- ❌ 不记录无 `@Log` 注解的方法调用

## 2. 用户故事
- 作为**审计员**，我可以查看所有用户的操作日志，以便追踪系统中的所有变更行为
- 作为**管理员**，我可以按操作人、业务类型筛选操作日志，以便定位特定用户或特定类型的操作
- 作为**管理员**，我可以导出操作日志为 Excel，以便离线存档或提交审计报告
- 作为**管理员**，我可以批量删除或清空过期操作日志，以便释放数据库存储空间

## 3. 功能需求

- FR-010-001: 系统 MUST 在带有 `@Log` 注解的 Controller 方法执行时，通过 AOP 切面自动捕获请求参数、响应结果、操作人、IP、浏览器、耗时等信息
- FR-010-002: 系统 MUST 通过 Spring 事件机制（`OperLogEvent`）异步持久化操作日志，不阻塞主业务流程
- FR-010-003: 系统 MUST 支持分页查询操作日志列表，支持按操作IP、模块标题、业务类型、状态、操作人、用户ID、部门ID、客户端、设备类型、浏览器、操作系统、时间区间等条件筛选
- FR-010-004: 系统 MUST 支持导出操作日志为 Excel（使用 Apache Fesod / EasyExcel），导出格式包含操作模块、业务类型、请求方法、请求方式、操作类别、操作人员、部门名称、请求地址、操作地址、操作地点、请求参数、状态、错误消息、消耗时间等字段
- FR-010-005: 系统 MUST 支持批量删除指定操作日志（按 operIds 数组）
- FR-010-006: 系统 MUST 支持清空全部操作日志，使用 `@Lock4j` 分布式锁防止并发清空
- FR-010-007: 系统 MUST 按 `oper_id` 降序排列日志列表（默认排序）
- FR-010-008: 系统 MUST 在记录异常日志时截断错误消息至 3800 字符，防止超长错误信息撑爆数据库字段

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| SysOperLog | 操作日志表 (sys_oper_log) | operId, title, businessType, method, operName, userId, deptId, operUrl, operIp, operParam, jsonResult, status, errorMsg, costTime, operTime |
| OperLogEvent | 操作日志事件 (Spring Event) | 与 SysOperLog 字段一致，由 LogAspect 构建并通过 Spring 事件总线发布 |

## 5. 验收场景

### 场景：操作日志自动记录
- Given 管理员登录系统，访问任意带有 `@Log` 注解的接口
- When 请求完成后
- Then `oper_log` 表中自动新增一条记录，包含操作人、IP、请求参数、响应结果、耗时等信息

### 场景：操作日志条件查询
- Given 管理员进入操作日志页面
- When 选择操作人"admin"、业务类型"修改"、时间范围"最近7天" → 点击查询
- Then 系统返回符合条件的操作日志分页列表

### 场景：导出操作日志
- Given 管理员在操作日志页面
- When 设置筛选条件后点击导出
- Then 系统生成 Excel 文件并触发浏览器下载，文件包含符合条件的所有操作日志

### 场景：清空操作日志
- Given 管理员拥有 `monitor:operlog:remove` 权限
- When 点击清空操作日志按钮
- Then 系统删除 `oper_log` 表中所有记录，返回成功

## 6. 非功能需求
- 操作日志记录 MUST 使用 `@Async` 异步执行，不得阻塞业务请求
- LogAspect 中异常捕获 MUST 不向业务层抛出，仅记录本地 `log.error`
- 请求参数和响应参数截断至 3800 字符，URL 截断至 255 字符
- Excel 导出使用 Apache Fesod（原 EasyExcel），支持字典翻译（如 `sys_oper_type`、`sys_common_status`）

## 7. 依赖
- ruoyi-common-log（`@Log` 注解、`LogAspect` 切面、`OperLogEvent` 事件）
- ruoyi-common-excel（Apache Fesod Excel 导出）
- ruoyi-common-security（Sa-Token `@SaCheckPermission` 权限控制）
- ruoyi-common-redis（`@Lock4j` 分布式锁）
- ruoyi-common-satoken（`LoginHelper` 获取当前登录用户信息）
