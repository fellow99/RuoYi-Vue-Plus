# 字典管理功能规格 (spec.md)

> 模块：007-dict | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
字典管理是系统基础数据模块，管理系统中经常使用的一些较为固定的数据，如性别、状态、类型等。通过字典类型与字典数据的父子结构，实现灵活的键值对配置。

### 1.2 解决的问题
- 管理员需要一个界面来维护系统中常用的固定数据项（如"用户性别"字典包含"男/女/未知"）
- 前端下拉框、单选按钮等组件需要动态获取字典数据进行渲染
- 字典数据变更后需要立即生效，不能要求重启服务

### 1.3 范围
- ✅ 字典类型 CRUD
- ✅ 字典数据 CRUD（归属字典类型，父子关系）
- ✅ 字典缓存管理（Redis 缓存 + 手动刷新）
- ✅ 字典数据按类型查询（供前端下拉框使用）
- ✅ Excel 导出
- ❌ 字典数据直接暴露给第三方系统

## 2. 用户故事
- 作为**管理员**，我可以新建一个字典类型（如"用户状态"），以便对该类字典数据进行归类管理
- 作为**管理员**，我可以在某个字典类型下添加多个字典数据（如"0=正常,1=停用"），以便前端组件引用
- 作为**管理员**，我可以刷新字典缓存，以便字典修改后立即在所有服务中生效

## 3. 功能需求

- FR-007-001: 系统 MUST 支持分页查询字典类型列表，支持按字典名称、字典类型筛选
- FR-007-002: 系统 MUST 支持新增字典类型，dictType 字段唯一
- FR-007-003: 系统 MUST 支持修改字典类型，修改 dictType 时同步更新其下所有字典数据的 dictType
- FR-007-004: 系统 MUST 支持删除字典类型，已分配字典数据的类型不可删除
- FR-007-005: 系统 MUST 支持分页查询字典数据列表，支持按字典标签、字典类型筛选
- FR-007-006: 系统 MUST 支持新增字典数据，同一 dictType 下 dictValue 唯一
- FR-007-007: 系统 MUST 支持根据字典类型查询该类型下所有字典数据（供前端下拉框使用）
- FR-007-008: 系统 MUST 在字典类型或数据变更时自动更新 Redis 缓存（CacheNames.SYS_DICT）
- FR-007-009: 系统 MUST 支持手动刷新全部字典缓存
- FR-007-010: 系统 MUST 支持导出字典类型和数据列表为 Excel

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| SysDictType | 字典类型主表 | dictId, dictName, dictType, remark |
| SysDictData | 字典数据子表 | dictCode, dictSort, dictLabel, dictValue, dictType, cssClass, listClass, isDefault, remark |

> 父子关系：SysDictData.dictType = SysDictType.dictType，一个字典类型下有多个字典数据

## 5. 验收场景

### 场景：新增字典类型与数据
- Given 管理员登录系统，进入字典管理
- When 新增字典类型"用户状态"，dictType="sys_user_status" → 在该类型下新增数据"正常"="0"、"停用"="1"
- Then 字典类型列表显示新类型，前端可通过 `/system/dict/data/type/sys_user_status` 获取数据列表

### 场景：修改字典类型同步数据
- Given 字典类型"用户状态"下有 2 条字典数据
- When 将 dictType 从 "sys_user_status" 修改为 "sys_user_status_new"
- Then 该类型下 2 条字典数据的 dictType 也自动更新为 "sys_user_status_new"，旧缓存清除

### 场景：删除已分配数据的字典类型被拒绝
- Given 字典类型"用户状态"下已存在字典数据
- When 尝试删除该字典类型
- Then 系统提示"xxx已分配,不能删除"，删除被拒绝

## 6. 非功能需求
- 字典数据 MUST 通过 Redis 缓存（CacheNames.SYS_DICT、CacheNames.SYS_DICT_TYPE），读取走缓存保证性能
- 字典类型删除前 MUST 检查是否有字典数据关联，防止脏数据
- 多值字典（如 "0,1,2"）MUST 支持按分隔符解析转换为对应的标签

## 7. 依赖
- Redis（缓存）— 字典数据缓存基于 Redisson + Spring Cache 注解
- ruoyi-common-redis（CacheUtils）— 缓存操作工具
