# 003-角色管理 - 数据模型

**模块编号：** 003  
**模块名称：** 角色管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 角色表 (sys_role)

**表名：** `sys_role`

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| role_id | BIGINT | - | YES | - | 角色 ID（主键） |
| tenant_id | VARCHAR | 20 | NO | '000000' | 租户编号 |
| role_name | VARCHAR | 30 | YES | - | 角色名称 |
| role_key | VARCHAR | 100 | YES | - | 角色标识 |
| role_sort | INT | - | YES | - | 显示顺序 |
| data_scope | CHAR | 1 | NO | '1' | 数据范围（1 全部 2 自定义 3 本部门 4 本部门及以下 5 仅本人） |
| menu_check_strictly | TINYINT | 1 | NO | 1 | 菜单父子关联 |
| dept_check_strictly | TINYINT | 1 | NO | 1 | 部门父子关联 |
| status | CHAR | 1 | NO | '0' | 状态（0 正常 1 停用） |
| del_flag | CHAR | 1 | NO | '0' | 删除标志 |
| create_by | BIGINT | - | NO | NULL | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | BIGINT | - | NO | NULL | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |
| remark | VARCHAR | 500 | NO | - | 备注 |

**索引：** PRIMARY KEY (`role_id`), KEY `idx_role_key` (`role_key`)

### 1.2 角色菜单关联表 (sys_role_menu)

| 字段名 | 类型 | 说明 |
|-------|------|------|
| role_id | BIGINT | 角色 ID |
| menu_id | BIGINT | 菜单 ID |

### 1.3 角色部门关联表 (sys_role_dept)

| 字段名 | 类型 | 说明 |
|-------|------|------|
| role_id | BIGINT | 角色 ID |
| dept_id | BIGINT | 部门 ID |

---

## 二、数据对象类

### 2.1 SysRole (DO)

```java
@TableName("sys_role")
public class SysRole extends TenantEntity {
    @TableId(value = "role_id")
    private Long roleId;
    private String roleName;
    private String roleKey;
    private Integer roleSort;
    private String dataScope;
    private Boolean menuCheckStrictly;
    private Boolean deptCheckStrictly;
    private String status;
    @TableLogic
    private String delFlag;
}
```

### 2.2 SysRoleBo (BO)

```java
@AutoMapper(target = SysRole.class)
public class SysRoleBo extends BaseEntity {
    private Long roleId;
    
    @NotBlank(message = "角色名称不能为空")
    @Size(min = 0, max = 30, message = "角色名称长度不能超过{max}个字符")
    private String roleName;
    
    @NotBlank(message = "权限字符不能为空")
    @Size(min = 0, max = 100, message = "权限字符长度不能超过{max}个字符")
    private String roleKey;
    
    private Integer roleSort;
    private String dataScope;
    private Long[] menuIds;
    private Long[] deptIds;
    private String status;
}
```

### 2.3 SysRoleVo (VO)

```java
@AutoMapper(target = SysRole.class)
public class SysRoleVo implements Serializable {
    private Long roleId;
    private String roleName;
    private String roleKey;
    private Integer roleSort;
    private String dataScope;
    private String status;
    private Date createTime;
}
```

---

## 三、数据状态说明

### 3.1 数据范围 (dataScope)

| 值 | 说明 |
|---|------|
| 1 | 全部数据权限 |
| 2 | 自定义数据权限 |
| 3 | 本部门数据权限 |
| 4 | 本部门及以下数据权限 |
| 5 | 仅本人数据权限 |

### 3.2 角色状态 (status)

| 值 | 说明 |
|---|------|
| 0 | 正常 |
| 1 | 停用 |

