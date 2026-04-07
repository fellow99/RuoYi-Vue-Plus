# 007-字典管理 - 数据模型

**模块编号：** 007  
**模块名称：** 字典管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 字典类型表 (sys_dict_type)

**表名：** `sys_dict_type`  
**说明：** 存储字典类型信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| dict_id | BIGINT | - | YES | - | 字典 ID（主键） |
| dict_name | VARCHAR | 100 | YES | - | 字典名称 |
| dict_type | VARCHAR | 100 | YES | - | 字典类型（唯一） |
| remark | VARCHAR | 500 | NO | - | 备注 |
| tenant_id | VARCHAR | 20 | NO | - | 租户 ID |
| create_by | BIGINT | - | NO | NULL | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | BIGINT | - | NO | NULL | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |

**索引：**
- PRIMARY KEY (`dict_id`)
- UNIQUE KEY `uk_dict_type` (`dict_type`)
- KEY `idx_dict_name` (`dict_name`)

### 1.2 字典数据表 (sys_dict_data)

**表名：** `sys_dict_data`  
**说明：** 存储字典数据信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| dict_code | BIGINT | - | YES | - | 字典编码（主键） |
| dict_sort | INT | - | NO | 0 | 字典排序 |
| dict_label | VARCHAR | 100 | YES | - | 字典标签 |
| dict_value | VARCHAR | 100 | YES | - | 字典键值 |
| dict_type | VARCHAR | 100 | YES | - | 字典类型 |
| css_class | VARCHAR | 100 | NO | - | 样式属性 |
| list_class | VARCHAR | 100 | NO | - | 表格样式 |
| is_default | CHAR | 1 | NO | 'N' | 是否默认（Y 是 N 否） |
| status | CHAR | 1 | NO | '0' | 状态（0 正常 1 停用） |
| remark | VARCHAR | 500 | NO | - | 备注 |
| tenant_id | VARCHAR | 20 | NO | - | 租户 ID |
| create_by | BIGINT | - | NO | NULL | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | BIGINT | - | NO | NULL | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |

**索引：**
- PRIMARY KEY (`dict_code`)
- KEY `idx_dict_type` (`dict_type`)
- KEY `idx_dict_sort` (`dict_sort`)
- KEY `idx_status` (`status`)

---

## 二、数据对象类

### 2.1 SysDictType (DO)

```java
@Data
@EqualsAndHashCode(callSuper = true)
@TableName("sys_dict_type")
public class SysDictType extends TenantEntity {
    @TableId(value = "dict_id")
    private Long dictId;
    private String dictName;
    private String dictType;
    private String remark;
}
```

### 2.2 SysDictData (DO)

```java
@Data
@EqualsAndHashCode(callSuper = true)
@TableName("sys_dict_data")
public class SysDictData extends TenantEntity {
    @TableId(value = "dict_code")
    private Long dictCode;
    private Integer dictSort;
    private String dictLabel;
    private String dictValue;
    private String dictType;
    private String cssClass;
    private String listClass;
    private String isDefault;
    private String status;
    private String remark;
}
```

### 2.3 SysDictTypeBo (BO)

```java
@AutoMapper(target = SysDictType.class)
public class SysDictTypeBo extends BaseEntity {
    private Long dictId;
    
    @NotBlank(message = "字典名称不能为空")
    @Size(min = 2, max = 100, message = "字典名称长度不能超过{max}个字符")
    private String dictName;
    
    @NotBlank(message = "字典类型不能为空")
    @Size(min = 2, max = 100, message = "字典类型长度不能超过{max}个字符")
    private String dictType;
    
    @Size(max = 500, message = "备注长度不能超过{max}个字符")
    private String remark;
}
```

### 2.4 SysDictDataBo (BO)

```java
@AutoMapper(target = SysDictData.class)
public class SysDictDataBo extends BaseEntity {
    private Long dictCode;
    
    @NotNull(message = "字典排序不能为空")
    private Integer dictSort;
    
    @NotBlank(message = "字典标签不能为空")
    @Size(min = 1, max = 100, message = "字典标签长度不能超过{max}个字符")
    private String dictLabel;
    
    @NotBlank(message = "字典键值不能为空")
    @Size(min = 1, max = 100, message = "字典键值长度不能超过{max}个字符")
    private String dictValue;
    
    @NotBlank(message = "字典类型不能为空")
    private String dictType;
    
    private String cssClass;
    private String listClass;
    private String isDefault;
    private String status;
    private String remark;
}
```

### 2.5 SysDictTypeVo (VO)

```java
@AutoMapper(target = SysDictType.class)
public class SysDictTypeVo implements Serializable {
    private Long dictId;
    private String dictName;
    private String dictType;
    private String remark;
}
```

### 2.6 SysDictDataVo (VO)

```java
@AutoMapper(target = SysDictData.class)
public class SysDictDataVo implements Serializable {
    private Long dictCode;
    private Integer dictSort;
    private String dictLabel;
    private String dictValue;
    private String dictType;
    private String cssClass;
    private String listClass;
    private String isDefault;
    private String status;
    private String remark;
}
```

---

## 三、常量定义

```java
public class SystemConstants {
    /**
     * 是否默认
     */
    public static final String YES = "Y";
    public static final String NO = "N";
    
    /**
     * 状态
     */
    public static final String NORMAL = "0";
    public static final String DISABLE = "1";
}
```

