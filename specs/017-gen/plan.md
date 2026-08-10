# 017-gen 技术方案 (plan.md)

> 对应规格：spec.md | 模块：017-gen | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17, FreeMarker 2.4
- dynamic-datasource 4.5.0（多数据源路由）
- Anyline（元数据引擎，支持 MySQL/Oracle/PostgreSQL/SQLServer）

### 1.2 依赖
- `ruoyi-common-core` — R 响应、基础工具
- `ruoyi-common-mybatis` — BaseMapperPlus、@DS、DataBaseHelper、IdGeneratorUtil
- `ruoyi-common-security` — @SaCheckPermission、@Lock4j
- `ruoyi-common-doc` — SpringDoc（生成的 Controller 模板带 Javadoc 注释）
- `ruoyi-common-web` — 基础 Web 层
- `freemarker` — 模板引擎（Hutool `TemplateEngine` 封装）
- `anyline-environment-spring-data-jdbc` + 对应 DB 驱动 — 元数据读取

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → IGenTableService/GenTableServiceImpl → GenTableMapper/GenTableColumnMapper → MySQL |
| 注解驱动 | ✅ | @SaCheckPermission、@Lock4j、@DS、@Log、@InterceptorIgnore |
| 模板驱动 | ✅ | FreeMarker 模板 + GenConstants 常量定义模板集 |

## 3. 数据模型

### 3.1 核心表
- `gen_table` — 生成表配置（继承 BaseEntity 审计字段）
- `gen_table_column` — 字段配置（继承 BaseEntity 审计字段）

### 3.2 关键字段规则
- `gen_table.dataName` — 数据源名称，用于 `@DS("#dataName")` 路由
- `gen_table.tableName` — 物理表名（唯一键组合 dataName + tableName）
- `gen_table.tplCategory` — 模板类别：`crud` 或 `tree`
- `gen_table.frontendType` — 前端技术栈：`vue` 或 `react`
- `gen_table_column.javaType` — 映射规则：varchar→String, int→Long, datetime→LocalDateTime 等
- `gen_table_column.queryType` — EQ/LIKE/BETWEEN/IS_NULL
- `gen_table_column.htmlType` — input/textarea/select/radio/datetime/imageUpload/editor
- `gen_table_column.isPk` — 主键标记，GenTable.getPkColumn() 返回主键列
- `gen_table_column.sort` — 字段排序、生成代码中字段顺序

### 3.3 GenProperties（generator.yml）
```yaml
gen:
  author: ruoyi
  packageName: org.dromara
  autoRemovePre: true
  tablePrefix: sys_
```

## 4. 接口契约

### 4.1 提供接口（/tool/gen）
| 方法 | 路径 | 说明 | 权限 |
|------|------|------|------|
| GET | /list | 分页查询生成表列表 | tool:gen:list |
| GET | /{tableId} | 查询生成表详情 | tool:gen:query |
| GET | /db/list | 查询数据库表列表（分页） | tool:gen:list |
| GET | /column/{tableId} | 查询表字段列表 | tool:gen:query |
| POST | /importTable | 导入表结构 | tool:gen:import |
| PUT | / | 修改生成配置 | tool:gen:edit |
| DELETE | /{tableIds} | 删除生成表 | tool:gen:remove |
| GET | /preview/{tableId} | 预览代码 | tool:gen:preview |
| GET | /download/{tableId} | 下载单表代码 ZIP | tool:gen:download |
| GET | /synchDb/{tableId} | 同步数据库结构 | tool:gen:edit |
| GET | /batchGenCode | 批量下载代码 ZIP | tool:gen:download |
| GET | /getDataNames | 获取数据源名称列表 | tool:gen:list |

### 4.2 消费接口
- `DataBaseHelper.getDataSourceNameList()` — 获取所有 dynamic-datasource 数据源名称
- `DataBaseHelper.getDataBaseType()` — 获取数据源数据库类型（用于选择 SQL 模板）
- `IdGeneratorUtil.nextId()` — 生成雪花 ID（菜单 ID）

## 5. 实现策略

### 5.1 架构模式
标准四层 + 模板引擎：GenController → IGenTableService/GenTableServiceImpl → GenTableMapper/GenTableColumnMapper → MySQL（gen_table/gen_table_column 元数据表）。

数据库元数据读取不走 Mapper 层，而是通过 Anyline 引擎的 `ServiceProxy.metadata()` 直接查询物理表结构。

### 5.2 关键算法
- **表导入流程**：
  1. `GenUtils.initTable(GenTable, GenProperties)` — 设置类名/包名/模块名等默认值
  2. `genTableMapper.insert(genTable)` — 插入 gen_table（雪花 ID 主键）
  3. Anyline `ServiceProxy.metadata().table(genTable.getTableName())` — 读取物理表元数据
  4. `GenUtils.initColumnField(GenTableColumn, columnMeta)` — 推断 Java 类型/查询方式/HTML 组件（基于字段名称规则）
  5. `genTableColumnMapper.insertBatch(columns)` — 批量插入字段配置
- **数据源路由**：`@DS("#genTable.dataName")` + `MyBatisDataSourceMonitor` 通知 Anyline 当前数据源
- **模板选择**：`TemplateEngineUtils.getTemplateList()` 根据 `tplCategory` + `frontendType` + `dbType` 选择模板集
- **代码生成**：Hutool `TemplateEngine` 渲染 → 写入 `ByteArrayOutputStream` → ZipUtil 打包 → `ServletOutputStream`

### 5.3 错误处理
- 数据源不存在 → DataBaseHelper 返回空列表，前端无数据源可选
- 表名重复导入 → 数据库唯一键约束异常
- 数据库连接失败 → `@DS` 切换失败，全局异常处理返回错误信息

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| GenController | REST API（12 个端点） | gen/controller/ |
| IGenTableService | 业务接口 | gen/service/ |
| GenTableServiceImpl | 业务实现（DS 路由、Anyline 元数据） | gen/service/impl/ |
| GenTable | 生成表实体 | gen/domain/ |
| GenTableColumn | 字段配置实体 | gen/domain/ |
| GenTableMapper | 数据访问（含 selectTableNameList） | gen/mapper/ |
| GenTableColumnMapper | 字段数据访问 | gen/mapper/ |
| GenUtils | 类型推断、初始化工具 | gen/util/ |
| TemplateEngineUtils | FreeMarker 引擎、上下文构建、模板选 | gen/util/ |
| PathNamedTemplate | 路径化模板包装器 | gen/util/template/ |
| GenConstants | 常量定义（类型映射、模板路径） | gen/constant/ |
| GenProperties | YAML 配置绑定 | gen/config/properties/ |
| MyBatisDataSourceMonitor | Anyline-DS 适配器 | gen/config/ |
| 20 个 .ftl 文件 | FreeMarker 模板 | resources/fm/{java,xml,vue,react,sql}/ |
| generator.yml | 全局生成配置 | resources/ |
| DataBaseHelper | 数据源名称/类型查询 | common-mybatis/helper/ |
