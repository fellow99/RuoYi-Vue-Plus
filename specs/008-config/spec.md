# 参数配置功能规格 (spec.md)

> 模块：008-config | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
参数配置是系统动态配置管理模块，以键值对形式管理系统的常用参数，如用户注册开关、文件上传大小限制等。管理员可在界面直接修改参数值而无需重启服务。

### 1.2 解决的问题
- 系统存在大量配置开关和阈值（如"是否允许注册"、"初始密码"），需要集中管理界面
- 修改配置后需要立即生效，不能要求重启或重新部署
- 业务代码需要按 configKey 快速查询配置值进行逻辑判断

### 1.3 范围
- ✅ 参数配置 CRUD
- ✅ 按 configKey 查询键值
- ✅ 缓存管理（Redis 缓存 + 手动刷新）
- ✅ Excel 导出
- ✅ 内置参数标记（configType），防止误删系统内置参数
- ❌ 参数版本历史

## 2. 用户故事
- 作为**管理员**，我可以新增一个参数配置（如"用户注册开关"=true），以便控制系统功能是否启用
- 作为**管理员**，我可以在界面修改参数值，修改后即刻生效无需重启
- 作为**管理员**，我可以刷新参数缓存，以确保所有服务读取到最新配置

## 3. 功能需求

- FR-008-001: 系统 MUST 支持分页查询参数配置列表，支持按参数名称、参数键名、内置类型筛选
- FR-008-002: 系统 MUST 支持新增参数配置，configKey 全局唯一
- FR-008-003: 系统 MUST 支持修改参数配置
- FR-008-004: 系统 MUST 支持按 configKey 查询参数值（供业务代码调用）
- FR-008-005: 系统 MUST 支持按 configKey 修改参数值
- FR-008-006: 系统 MUST 支持批量删除参数配置
- FR-008-007: 系统 MUST 在参数变更时自动更新 Redis 缓存（CacheNames.SYS_CONFIG）
- FR-008-008: 系统 MUST 支持手动刷新参数缓存
- FR-008-009: 系统 MUST 支持导出参数配置列表为 Excel

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| SysConfig | 参数配置表 | configId, configName, configKey, configValue, configType, remark |

> configType: Y=系统内置参数（不可随意删除），N=用户自定义参数

## 5. 验收场景

### 场景：通过参数控制注册开关
- Given 管理员在参数配置中设置 `sys.account.registerUser = true`
- When 用户访问注册页面 → 提交注册信息
- Then 系统判断 `selectRegisterEnabled()` 返回 true，允许注册完成

### 场景：修改内置参数
- Given 参数"用户管理-账号初始密码"的 configType 为 Y
- When 管理员修改其 configValue → 保存
- Then 参数值更新，新创建用户使用新的初始密码

### 场景：新增参数键名重复
- Given 系统中已存在 configKey="sys.account.registerUser"
- When 管理员新增参数，配置相同的 configKey
- Then 系统提示"新增参数失败，参数键名已存在"

## 6. 非功能需求
- 参数值 MUST 通过 Redis 缓存（CacheNames.SYS_CONFIG），读取走缓存保证性能
- selectConfigByKey() MUST 从缓存读取，避免每次业务判断查询数据库
- 缓存刷新操作 MUST 使用 @Lock4j 分布式锁防止并发重复刷新

## 7. 依赖
- Redis（缓存）— 参数缓存基于 Redisson + Spring Cache 注解
- ruoyi-common-redis（CacheUtils）— 缓存操作工具
