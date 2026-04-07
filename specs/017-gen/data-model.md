# 017-代码生成 - 数据模型

**模块编号：** 017  
**模块名称：** 代码生成  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 代码生成业务表 (gen_table)

**表名：** `gen_table`  
**说明：** 存储代码生成的表配置信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| table_id | BIGINT | - | YES | - | 编号（主键） |
| data_name | VARCHAR | 50 | YES | - | 数据源名称（Plus 新增） |
| table_name | VARCHAR | 200 | YES | - | 表名称 |
| table_comment | VARCHAR | 500 | YES | - | 表描述 |
| sub_table_name | VARCHAR | 64 | NO | - | 关联父表的表名（主子表用） |
| sub_table_fk_name | VARCHAR | 64 | NO | - | 本表关联父表的外键名（主子表用） |
| class_name | VARCHAR | 100 | YES | - | 实体类名称（首字母大写） |
| tpl_category | VARCHAR | 200 | NO | 'crud' | 使用的模板（crud/tree） |
| package_name | VARCHAR | 100 | YES | - | 生成包路径 |
| module_name | VARCHAR | 30 | YES | - | 生成模块名 |
| business_name | VARCHAR | 30 | YES | - | 生成业务名 |
| function_name | VARCHAR | 50 | YES | - | 生成功能名 |
| function_author | VARCHAR | 50 | YES | - | 生成作者 |
| gen_type | VARCHAR | 1 | NO | '0' | 生成代码方式（0zip 压缩包 1 自定义路径） |
| gen_path | VARCHAR | 200 | NO | - | 生成路径（不填默认项目路径） |
| options | VARCHAR | 1000 | NO | - | 其它生成选项（JSON 格式） |
| create_by | VARCHAR | 64 | NO | - | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | VARCHAR | 64 | NO | - | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |
| remark | VARCHAR | 500 | NO | - | 备注 |

**索引：**
- PRIMARY KEY (`table_id`)
- KEY `idx_table_name` (`table_name`)

**约束：**
- `data_name` + `table_name` 联合唯一

**与 RuoYi-Vue 的差异：**
| 字段 | RuoYi-Vue | RuoYi-Vue-Plus | 说明 |
|------|-----------|----------------|------|
| data_name | 无 | VARCHAR(50) | 多数据源支持 |
| create_by/update_by | VARCHAR | VARCHAR | 保持兼容 |

**options 字段 JSON 结构：**
```json
{
  "treeCode": "dept_id",
  "treeParentCode": "parent_id",
  "treeName": "dept_name",
  "parentMenuId": "3",
  "parentMenuName": "系统工具"
}
```

---

### 1.2 代码生成业务字段表 (gen_table_column)

**表名：** `gen_table_column`  
**说明：** 存储代码生成的字段配置信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| column_id | BIGINT | - | YES | - | 编号（主键） |
| table_id | BIGINT | - | YES | - | 归属表编号 |
| column_name | VARCHAR | 200 | YES | - | 列名称 |
| column_comment | VARCHAR | 500 | NO | - | 列描述 |
| column_type | VARCHAR | 100 | NO | - | 列类型 |
| java_type | VARCHAR | 500 | NO | - | JAVA 类型 |
| java_field | VARCHAR | 200 | YES | - | JAVA 字段名 |
| is_pk | CHAR | 1 | NO | '0' | 是否主键（1 是） |
| is_increment | CHAR | 1 | NO | '0' | 是否自增（1 是） |
| is_required | CHAR | 1 | NO | '0' | 是否必填（1 是） |
| is_insert | CHAR | 1 | NO | '0' | 是否为插入字段（1 是） |
| is_edit | CHAR | 1 | NO | '0' | 是否编辑字段（1 是） |
| is_list | CHAR | 1 | NO | '0' | 是否列表字段（1 是） |
| is_query | CHAR | 1 | NO | '0' | 是否查询字段（1 是） |
| query_type | VARCHAR | 200 | NO | 'EQ' | 查询方式（EQ/NE/GT/LT/LIKE/BETWEEN） |
| html_type | VARCHAR | 200 | NO | - | 显示类型（input/textarea/select 等） |
| dict_type | VARCHAR | 200 | NO | - | 字典类型 |
| sort | INT | - | NO | 0 | 排序 |
| create_by | VARCHAR | 64 | NO | - | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | VARCHAR | 64 | NO | - | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |

**索引：**
- PRIMARY KEY (`column_id`)
- KEY `idx_table_id` (`table_id`)

**约束：**
- FOREIGN KEY (`table_id`) REFERENCES `gen_table`(`table_id`)

---

## 二、领域模型

### 2.1 GenTable（业务表实体）

**类名：** `org.dromara.generator.domain.GenTable`  
**父类：** `BaseEntity`  
**注解：** `@TableName("gen_table")`

**核心属性：**
- `tableId`: Long - 编号
- `dataName`: String - 数据源名称
- `tableName`: String - 表名称
- `tableComment`: String - 表描述
- `className`: String - 实体类名称
- `tplCategory`: String - 模板类型
- `packageName`: String - 包路径
- `moduleName`: String - 模块名
- `businessName`: String - 业务名
- `functionName`: String - 功能名
- `functionAuthor`: String - 作者
- `genType`: String - 生成方式
- `genPath`: String - 生成路径
- `columns`: List<GenTableColumn> - 字段列表
- `pkColumn`: GenTableColumn - 主键字段

**业务方法：**
- `isTree()`: boolean - 是否为树表模板
- `isCrud()`: boolean - 是否为 CRUD 模板
- `isSuperColumn(javaField)`: boolean - 是否为基类字段

### 2.2 GenTableColumn（业务字段实体）

**类名：** `org.dromara.generator.domain.GenTableColumn`  
**父类：** `BaseEntity`  
**注解：** `@TableName("gen_table_column")`

**核心属性：**
- `columnId`: Long - 编号
- `tableId`: Long - 归属表 ID
- `columnName`: String - 列名称
- `columnComment`: String - 列描述
- `columnType`: String - 列类型
- `javaType`: String - Java 类型
- `javaField`: String - Java 字段名
- `isPk`: String - 是否主键
- `isIncrement`: String - 是否自增
- `isRequired`: String - 是否必填
- `isInsert`: String - 是否插入字段
- `isEdit`: String - 是否编辑字段
- `isList`: String - 是否列表字段
- `isQuery`: String - 是否查询字段
- `queryType`: String - 查询方式
- `htmlType`: String - 显示类型
- `dictType`: String - 字典类型
- `sort`: Integer - 排序

**业务方法：**
- `getCapJavaField()`: String - 首字母大写的 Java 字段名
- `isPk()`: boolean - 是否主键
- `isIncrement()`: boolean - 是否自增
- `isRequired()`: boolean - 是否必填
- `isInsert()`: boolean - 是否插入字段
- `isEdit()`: boolean - 是否编辑字段
- `isList()`: boolean - 是否列表字段
- `isQuery()`: boolean - 是否查询字段
- `isSuperColumn()`: boolean - 是否为基类字段
- `readConverterExp()`: String - 读取字典数据

---

## 三、数据传输对象

### 3.1 查询参数

**GenTableQuery:**
- `dataName`: String - 数据源名称
- `tableName`: String - 表名称（模糊）
- `tableComment`: String - 表描述（模糊）
- `beginTime`: String - 开始时间
- `endTime`: String - 结束时间

### 3.2 响应数据

**GenTableVO:**
- 包含 GenTable 所有字段
- `columns`: List<GenTableColumn> - 字段列表
- `pkColumn`: GenTableColumn - 主键信息

**GenTableColumnVO:**
- 包含 GenTableColumn 所有字段

**CodePreviewVO:**
- `fileName`: String - 文件名
- `content`: String - 文件内容

---

## 四、数据关系

```
gen_table (1) ──→ (N) gen_table_column
    │
    └── data_name ──→ 多数据源配置
```

---

## 五、数据流转

### 5.1 导入流程

```
数据库表 → 读取元数据 → GenTable/GenTableColumn → 保存到 gen_table/gen_table_column
```

### 5.2 生成流程

```
gen_table + gen_table_column → Velocity 模板 → 代码文件 → ZIP/目录
```

### 5.3 同步流程

```
数据库表变更 → 对比差异 → 更新 gen_table_column → 保留用户配置
```

---

**文档版本：** 1.0  
**创建日期：** 2026-03-13  
**审核状态：** 待审核
