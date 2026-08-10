# API 文档功能规格 (spec.md)

> 模块：018-swagger | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
API 文档模块基于 SpringDoc（OpenAPI 3）和 Therapi Runtime Javadoc，实现零注解、全自动的 API 接口文档生成。开发人员只需编写标准的 Java 类/方法注释，系统即可自动生成分组、格式化的 OpenAPI 文档。

### 1.2 解决的问题
- 开发人员不想为 Swagger 编写大量注解（@Api、@ApiOperation、@ApiModel 等），增加维护负担
- 需要自动将 Javadoc 注释转换为 API 文档摘要和描述
- 需要按业务模块自动分组 API（演示/通用/系统/代码生成/工作流），避免单一大文档
- 需要自动将 Sa-Token 权限注解（@SaCheckPermission、@SaCheckRole）渲染到文档中
- 旧版 Springfox（Swagger 2）已停止维护，需要迁移到 SpringDoc（OpenAPI 3）

### 1.3 范围
- ✅ SpringDoc OpenAPI 3 自动文档生成
- ✅ Therapi 运行时 Javadoc 读取（编译期序列化到 class 文件）
- ✅ 控制器类 Javadoc 自动作为 API 分组 Tag 名称
- ✅ 方法 Javadoc 自动作为 API 摘要和描述
- ✅ 多分组（按包名自动分组：演示/通用/系统/代码生成/工作流）
- ✅ Sa-Token 权限注解自动转换为文档中的"访问权限"说明
- ✅ 全局 context-path 路径前缀自动适配
- ✅ 安全配置（API 文档端点排除 Sa-Token 登录拦截）
- ❌ Swagger UI 界面（使用 springdoc-openapi-starter-webmvc-api 不含 UI，前端自行展示）
- ❌ Knife4j 增强（暂无集成）

## 2. 用户故事
- 作为**前端开发人员**，我可以访问 `/v3/api-docs` 获取完整的 OpenAPI 规范，以便生成 TypeScript 类型和 API 调用函数
- 作为**后端开发人员**，我编写好 Java 类注释后无需任何额外注解，API 文档即自动包含类名、方法名、参数说明
- 作为**测试人员**，我可以在 API 文档中查看每个接口需要哪些权限（@SaCheckPermission），以便构造测试用例
- 作为**项目管理员**，我可以按模块分组查看不同业务的 API 接口，避免所有接口混在一起

## 3. 功能需求

- FR-018-001: 系统 MUST 基于 SpringDoc 自动生成 OpenAPI 3 规范文档，API 路径为 `/v3/api-docs`
- FR-018-002: 系统 MUST 自动从 Javadoc 注释中提取控制器类描述作为 API 分组名称（Tag），提取方法注释作为 API 摘要和描述
- FR-018-003: 系统 MUST 支持全局 context-path 路径前缀自动添加到所有 API 路径上（避免重复添加）
- FR-018-004: 系统 MUST 支持通过 YAML 配置声明多分组（group-configs），每组对应一个业务包路径
- FR-018-005: 系统 MUST 自动读取 Sa-Token 权限注解（@SaCheckPermission、@SaCheckRole、@SaIgnore）并生成为文档中的"访问权限"区域
- FR-018-006: 系统 MUST 支持通过 `springdoc.api-docs.enabled` 属性控制文档开关（默认启用）
- FR-018-007: 系统 MUST 将 API 文档端点（`/*/api-docs`）排除出 Sa-Token 登录拦截，允许公开访问
- FR-018-008: 系统 MUST 支持通过 YAML 配置文档标题、描述、版本、联系人等基本信息
- FR-018-009: 系统 MUST 在编译期通过 `therapi-runtime-javadoc-scribe` 注解处理器将 Javadoc 序列化到 class 文件中
- FR-018-010: 系统 MUST 支持全局安全方案声明（SecurityScheme），自动应用到所有 API 操作

## 4. 关键实体

| 实体 | 说明 | 关键属性 |
|------|------|----------|
| SpringDocProperties | YAML 配置绑定 | info(title/description/version/contact), externalDocs, tags, components(securitySchemes) |
| OpenAPI | SpringDoc 根文档对象 | info, externalDocs, tags, paths, security |
| GroupedOpenApi | 分组定义（每个 group-configs 条目） | group（分组名）, packagesToScan（扫描包路径） |
| SaTokenSecurityMetadata | 权限元数据 | permissions, roles, ignore, toMarkdownString() |

## 5. 验收场景

### 场景：查看按模块分组的 API 文档
- Given 系统已启动，配置了 5 个分组
- When 访问 `/v3/api-docs/swagger-config` 获取分组配置
- Then 返回 5 个分组的 URL：`/v3/api-docs/1.演示模块`、`/v3/api-docs/2.通用模块` 等

### 场景：Controller Javadoc 自动成为 Tag
- Given 某 Controller 类有以下 Javadoc：`/** 用户管理 */`
- When 访问该模块的 api-docs
- Then Tag 名称为"用户管理"，而非自动生成的驼峰小写类名

### 场景：方法 Javadoc 自动成为摘要
- Given 某方法有以下 Javadoc：`/** 根据用户ID查询用户信息 */`
- When 查看该接口的文档
- Then `summary` 为"根据用户ID查询用户信息"，`description` 为空或无额外内容

### 场景：权限注解渲染到文档
- Given 某方法标注 `@SaCheckPermission("system:user:list")`
- When 查看该接口的文档
- Then description 区域包含 "访问权限" 信息块，显示需要的权限值

### 场景：未登录访问 API 文档
- Given 用户未登录
- When 直接访问 `/v3/api-docs`
- Then 返回完整的 OpenAPI JSON（不被 Sa-Token 拦截）

## 6. 非功能需求
- API 文档生成 MUST 零性能开销（文档在首次请求时构建，后续缓存）
- 编译期 Javadoc 序列化 MUST 不影响正常编译流程（therapi-scribe 作为注解处理器运行）
- 文档模块 MUST 通过 `springdoc-openapi-starter-webmvc-api` 引入（不含 UI），不增加前端包体积

## 7. 依赖
- `springdoc-openapi-starter-webmvc-api` (3.0.3) — OpenAPI 3 自动生成
- `therapi-runtime-javadoc` (0.15.0) — 运行时读取 Javadoc
- `therapi-runtime-javadoc-scribe` — 编译期注解处理器（序列化 Javadoc 到 class 文件）
- `jackson-module-kotlin` — 序列化兼容（SpringDoc 依赖）
- `ruoyi-common-core` — 基础工具
- `ruoyi-common-security` — 排除 API 文档路径的 Sa-Token 拦截器
