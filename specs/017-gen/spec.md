# 代码生成功能规格 (spec.md)

> 模块：017-gen | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
代码生成模块提供可视化、模板驱动的代码自动生成功能。用户只需设计好数据库表结构，系统即可一键生成前后端全套 CRUD 代码（Java/Vue/React/SQL），大幅降低重复开发工作量。

### 1.2 解决的问题
- 开发人员需要为每个新表编写重复的 CRUD 代码（Entity、Mapper、Service、Controller、前端页面）
- 需要支持多数据源导入表结构（主库 + 其他业务库）
- 需要灵活配置字段的查询方式、显示类型、字典关联等
- 需要支持 Vue（ElementPlus）和 React（Ant Design）两种前端技术栈

### 1.3 范围
- ✅ 多数据源表结构导入（通过 dynamic-datasource 动态切换）
- ✅ 字段配置（Java 类型、查询方式、HTML 组件类型、字典关联）
- ✅ 代码预览（HTML 格式语法高亮）
- ✅ 代码下载（单表和批量的 ZIP 文件）
- ✅ 数据库同步（DDL 变更后增量同步字段）
- ✅ 支持 CRUD 和 Tree 两种模板类别
- ✅ 支持 Vue 和 React 前端模板
- ✅ 自动生成菜单 SQL
- ❌ 多表关联生成（仅支持单表 CRUD）
- ❌ 自定义模板上传（使用内置 FreeMarker 模板）

## 2. 用户故事
- 作为**开发人员**，我可以选择任意数据源、导入目标表结构，以便系统自动分析字段信息
- 作为**开发人员**，我可以配置每个字段的查询方式（等于/模糊/范围）、显示类型（文本框/下拉/日期/富文本），以便生成的页面符合业务需求
- 作为**开发人员**，我可以预览即将生成的代码，以便确认代码风格和内容
- 作为**开发人员**，我可以一键下载全部代码（包括 Entity、Mapper、Service、Controller、前端页面、菜单 SQL），以便快速集成到项目中
- 作为**开发人员**，我可以批量选择多张表同时生成代码，以便一次性完成整个业务模块的搭建

## 3. 功能需求

- FR-017-001: 系统 MUST 支持分页查询多数据源的数据库表列表，排除系统内置表（sai_/sj_/flow_/gen_ 前缀）
- FR-017-002: 系统 MUST 支持将所选表的结构导入到 `gen_table` 和 `gen_table_column` 元数据表中
- FR-017-003: 系统 MUST 在导入时自动推断字段的 Java 类型、查询方式、HTML 组件类型（根据字段名/类型规则）
- FR-017-004: 系统 MUST 支持编辑生成配置（类名、包名、模块名、业务名、功能名、作者、生成模板类别、前端类型）
- FR-017-005: 系统 MUST 支持编辑每个字段的配置（Java 类型、查询方式、显示类型、字典类型、是否必填/插入/编辑/列表/查询）
- FR-017-006: 系统 MUST 支持预览生成的代码内容（HTML 格式，去掉外层 pre/code 标签直接预览）
- FR-017-007: 系统 MUST 支持下载单表代码为 ZIP 文件，名称为 `ruoyi.zip`
- FR-017-008: 系统 MUST 支持批量下载多表代码（按表名分子目录）为 ZIP 文件
- FR-017-009: 系统 MUST 支持数据库结构同步（DDL 变更后增量同步：新增列追加、删除列标记为只读）
- FR-017-010: 系统 MUST 支持 CRUD 和 Tree 两种模板类别（Tree 包含树形组件）
- FR-017-011: 系统 MUST 支持 Vue（ElementPlus）和 React（Ant Design）两种前端模板
- FR-017-012: 系统 MUST 根据目标数据库类型生成对应的菜单 SQL（MySQL/Oracle/PostgreSQL/SQLServer）
- FR-017-013: 系统 MUST 提供可用数据源名称列表接口（供前端下拉选择）
- FR-017-014: 系统 MUST 对导入和同步操作使用分布式锁保护（防止并发修改元数据）

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| GenTable | 生成表配置 | tableId, dataName（数据源）, tableName, tableComment, className, tplCategory（crud/tree）, frontendType（vue/react）, packageName, moduleName, businessName, functionName, functionAuthor, options |
| GenTableColumn | 字段配置 | columnId, tableId, columnName, columnComment, columnType, javaType, javaField, isPk, isIncrement, isRequired, isInsert, isEdit, isList, isQuery, queryType, htmlType, dictType, sort |
| GenProperties | 全局生成配置 | author, packageName, autoRemovePre（自动移除表前缀）, tablePrefix（表前缀列表） |
| MyBatisDataSourceMonitor | Anyline 数据源适配 | 桥接 Anyline 元数据引擎与 dynamic-datasource |

## 5. 验收场景

### 场景：导入表结构并生成代码
- Given 管理员在"代码生成"页面
- When 选择目标数据源 → 查询数据库表列表 → 勾选目标表 → 点击"导入"
- Then 表结构导入成功，自动分析出字段类型和默认配置，出现在生成列表

### 场景：预览和下载代码
- Given 已导入表且配置了字段信息
- When 点击"预览" → 查看生成代码内容 → 确认无误 → 点击"生成代码"
- Then 浏览器下载 `ruoyi.zip`，解压后可见 Entity、Mapper、Service、Controller 等完整 Java 文件

### 场景：数据库字段变更同步
- Given 数据库表新增了一个字段
- When 在代码生成列表中点击该表的"同步"
- Then 系统自动检测新字段并追加到字段列表中，已删除的字段保留但标记为只读

### 场景：批量生成
- Given 已导入多张表
- When 勾选多张表 → 点击"批量生成"
- Then 下载 ZIP 文件，内部按表名分子目录，每个目录包含该表完整代码

## 6. 非功能需求
- 代码生成 MUST 使用 FreeMarker 2.4 模板引擎，模板存放于 `classpath:fm/` 目录
- 数据库元数据读取 MUST 使用 Anyline 元数据引擎（`ServiceProxy.metadata()`），支持 MySQL、Oracle、PostgreSQL、SQLServer
- 多数据源切换 MUST 通过 `@DS` 注解 + SpEL 表达式实现（`@DS("#genTable.dataName")`）
- 导入与同步操作 MUST 使用 Lock4j 分布式锁防止并发（`@Lock4j` 注解）
- 生成代码 ZIP 下载 MUST 在 Controller 层直接操作 HttpServletResponse 输出流
- 雪花 ID（Snowflake）MUST 用于 gen_table 主键和生成的菜单 ID

## 7. 依赖
- `ruoyi-common-mybatis` — BaseMapperPlus、MyBatis-Plus、dynamic-datasource、DataBaseHelper
- `ruoyi-common-security` — Sa-Token 权限控制、Lock4j 分布式锁
- `ruoyi-common-doc` — SpringDoc 自动 API 注解（生成的 Controller 模板包含）
- FreeMarker (`freemarker`) — 模板引擎
- Anyline (`anyline-environment-spring-data-jdbc` + 数据库驱动) — 元数据读取引擎
- Hutool（`hutool-extra`）— FreeMarker 封装（`TemplateEngine`）
