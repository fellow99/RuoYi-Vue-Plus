# 模块编号 - 数据模型

**模块编号：** [MODULE_ID]  
**模块名称：** [MODULE_NAME]  
**版本：** 5.5.3 (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 主表

**表名：** 根据模块定义  
**说明：** 模块主数据表

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| id | BIGINT | - | YES | - | 主键 ID |
| create_by | VARCHAR | 64 | NO | - | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | VARCHAR | 64 | NO | - | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |
| tenant_id | VARCHAR | 20 | NO | - | 租户 ID |
| del_flag | CHAR | 1 | NO | 0 | 删除标志 |

**索引：**
- PRIMARY KEY (`id`)

---

## 二、配置表

### 2.1 配置表

**表名：** 根据模块定义  
**说明：** 模块配置表

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| config_key | VARCHAR | 100 | YES | - | 配置键 |
| config_value | TEXT | - | NO | NULL | 配置值 |
| description | VARCHAR | 500 | NO | - | 描述 |

---

**最后更新：** 2026-03-13  
**文档状态：** ✅ 完成
