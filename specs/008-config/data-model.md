# 008-参数配置 - 数据模型

**模块编号：** 008  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 参数配置表 (sys_config)

| 字段名 | 类型 | 长度 | 必填 | 说明 |
|-------|------|------|------|------|
| config_id | BIGINT | - | YES | 参数主键 |
| config_name | VARCHAR | 100 | YES | 参数名称 |
| config_key | VARCHAR | 100 | YES | 参数键名（唯一） |
| config_value | VARCHAR | 500 | YES | 参数键值 |
| config_type | CHAR | 1 | NO | 系统内置（Y 是 N 否） |
| remark | VARCHAR | 500 | NO | 备注 |
| tenant_id | VARCHAR | 20 | NO | 租户 ID |
| create_by/create_time/update_by/update_time | - | - | NO | 审计字段 |

**索引：** PRIMARY KEY (config_id), UNIQUE KEY uk_config_key (config_key)

---

## 二、数据对象类

### 2.1 SysConfig (DO)

```java
@Data
@TableName("sys_config")
public class SysConfig extends TenantEntity {
    @TableId(value = "config_id")
    private Long configId;
    private String configName;
    private String configKey;
    private String configValue;
    private String configType;
    private String remark;
}
```

### 2.2 SysConfigBo (BO)

```java
@AutoMapper(target = SysConfig.class)
public class SysConfigBo extends BaseEntity {
    @NotBlank(message = "参数名称不能为空")
    private String configName;
    
    @NotBlank(message = "参数键名不能为空")
    private String configKey;
    
    @NotBlank(message = "参数键值不能为空")
    private String configValue;
    
    private String configType;
    private String remark;
}
```

### 2.3 SysConfigVo (VO)

```java
@AutoMapper(target = SysConfig.class)
public class SysConfigVo implements Serializable {
    private Long configId;
    private String configName;
    private String configKey;
    private String configValue;
    private String configType;
    private String remark;
}
```

