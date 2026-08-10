# 018-swagger 技术方案 (plan.md)

> 对应规格：spec.md | 模块：018-swagger | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, SpringDoc 3.0.3 (OpenAPI 3)
- Therapi Runtime Javadoc 0.15.0

### 1.2 依赖
- `springdoc-openapi-starter-webmvc-api` — OpenAPI 3 自动配置（不含 swagger-ui）
- `therapi-runtime-javadoc` — 运行时读取 Javadoc 注释
- `therapi-runtime-javadoc-scribe` — 编译期 Javadoc 序列化（Maven 注解处理器）
- `jackson-module-kotlin` — Kotlin 类型兼容
- `ruoyi-common-core` — 基础依赖

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 注解驱动 | ✅（零注解） | 不使用 Swagger 注解，完全依赖标准 Javadoc |
| 组件化 | ✅ | 独立 `ruoyi-common-doc` 模块，通过 BOM 管理 |
| 安全优先 | ✅ | API 文档端点排除 Sa-Token 拦截 |

## 3. 数据模型

### 3.1 配置模型（application.yml）
```yaml
springdoc:
  api-docs:
    enabled: true
  info:
    title: RuoYi-Vue-Plus API 文档
    description: 基于 SpringDoc + Therapi 自动生成
    version: 6.0.0
    contact:
      name: dromara
      url: https://plus-doc.dromara.org
  group-configs:
    - group: "1.演示模块"
      packages-to-scan: org.dromara.demo
    - group: "2.通用模块"
      packages-to-scan: org.dromara.web
    - group: "3.系统模块"
      packages-to-scan: org.dromara.system
    - group: "4.代码生成模块"
      packages-to-scan: org.dromara.gen
    - group: "5.工作流模块"
      packages-to-scan: org.dromara.workflow
```

### 3.2 编译期 Javadoc 序列化
```xml
<!-- root pom.xml maven-compiler-plugin -->
<annotationProcessorPaths>
  <path>
    <groupId>com.github.therapi</groupId>
    <artifactId>therapi-runtime-javadoc-scribe</artifactId>
    <version>${therapi-javadoc.version}</version>
  </path>
</annotationProcessorPaths>
```

## 4. 接口契约

### 4.1 提供端点（SpringDoc 自动注册）
- `GET /v3/api-docs` — 完整 OpenAPI JSON
- `GET /v3/api-docs/{group}` — 分组文档（如 `/v3/api-docs/3.系统模块`）
- `GET /v3/api-docs/swagger-config` — 分组配置列表
- 权限：公开（`/*/api-docs` 已排除 Sa-Token 拦截）

### 4.2 消费接口
- 前端 API 代码生成工具（如 openapi-typescript-codegen）
- 测试工具（如 Postman、Insomnia 导入 OpenAPI）

## 5. 实现策略

### 5.1 架构模式
独立模块 + 自动配置注入：`ruoyi-common-doc` 提供 `SpringDocConfig` 自动配置类（通过 `AutoConfiguration.imports` 注册），被 `ruoyi-admin`、`ruoyi-system`、`ruoyi-gen`、`ruoyi-demo`、`ruoyi-workflow` 等模块依赖引入。

### 5.2 关键组件
- **SpringDocConfig** — 核心自动配置，注册以下 Bean：
  - `openApi(SpringDocProperties)` — 构建 OpenAPI 根对象，从 YAML 读取 info/tags/securitySchemes
  - `openApiCustomizer()` — `GlobalOpenApiCustomizer`，通过内部类 `PlusPaths` 给所有路径加 context-path 前缀（防重复）
  - `classTagOperationCustomizer(...)` — 替换 SpringDoc 自动生成的驼峰类名 Tag 为 Javadoc 第一行文本
  - `javadocOperationCustomizer(...)` — 将方法 Javadoc 设置为 summary/description，并调用所有 JavadocResolver 追加内容
  - `saTokenAnnotationJavadocResolver()` — 解析 Sa-Token 权限注解并追加到文档
  - 条件：`springdoc.api-docs.enabled=true`（默认启用）
- **ClassTagOperationCustomizer** — 实现 `GlobalOperationCustomizer` + `GlobalOpenApiCustomizer`：
  - 读取控制器类的 Javadoc → 第一行 = Tag name，全文 = Tag description
  - 如果类上已有 `@Tag`/`@Tags` 注解则使用注解
  - 清理自动生成的孤儿 tag
- **JavadocOperationCustomizer** — 实现 `GlobalOperationCustomizer`：
  - `JavadocProvider.getMethodJavadocDescription(handlerMethod)` → 第一句 = summary，全文 = description
  - 迭代所有 `JavadocResolver` bean，调用 `resolve(handlerMethod, operation)` 追加内容
- **SaTokenAnnotationMetadataJavadocResolver** — 实现 `JavadocResolver`：
  - 通过类加载器加载 Sa-Token 注解类（`SaCheckPermission`, `SaCheckRole`, `SaIgnore`, `SaCheckLogin`）
  - 读取方法级和类级注解的值 → 构建 `SaTokenSecurityMetadata` → `toMarkdownString()` 输出
  - 使用 classloader 加载避免硬依赖 Sa-Token，保持模块解耦
- **全局 Context-Path 适配**：
  - `openApiCustomizer` 中的 `PlusPaths` 内部类检测已添加前缀路径并跳过重复添加

### 5.3 安全策略
- `security.excludes` 包含 `/*/api-docs` 和 `/*/api-docs/**`
- Sa-Token `SaInterceptor` 对这些路径不做登录校验
- 实际 REST API 端点仍受 Sa-Token 保护

### 5.4 模块注册方式
每个需要生成文档的业务模块只需在 pom.xml 中添加 `ruoyi-common-doc` 依赖。SpringDoc 自动扫描该模块的控制器并生成文档。

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SpringDocConfig | 核心自动配置类 | ruoyi-common-doc/.../config/ |
| SpringDocProperties | springdoc.* 配置绑定 | ruoyi-common-doc/.../config/properties/ |
| ClassTagOperationCustomizer | Javadoc 驱动的 Tag 生成 | ruoyi-common-doc/.../core/customizer/ |
| JavadocOperationCustomizer | Javadoc 驱动的摘要/描述 | ruoyi-common-doc/.../core/customizer/ |
| SaTokenSecurityMetadata | 权限元数据模型 | ruoyi-common-doc/.../core/model/ |
| SaTokenAnnotationMetadataJavadocResolver | Sa-Token 权限注解解析 | ruoyi-common-doc/.../core/resolver/ |
| JavadocResolver | 解析器扩展接口 | ruoyi-common-doc/.../core/resolver/ |
| AbstractMetadataJavadocResolver | 解析器基类 | ruoyi-common-doc/.../core/resolver/ |
| AutoConfiguration.imports | 自动配置注册 | ruoyi-common-doc/.../META-INF/spring/ |
| application.yml | group-configs + springdoc 配置 | ruoyi-admin/src/main/resources/ |
| pom.xml (root) | therapi.version / springdoc.version + annotationProcessorPaths | / |
