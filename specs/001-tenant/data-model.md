# 001-租户管理 - 数据模型

**模块编号：** 001  
**模块名称：** 租户管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 租户表 (sys_tenant)

**表名：** `sys_tenant`  
**说明：** 存储系统租户基本信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| id | BIGINT | - | YES | - | 主键 ID |
| tenant_id | VARCHAR | 20 | YES | - | 租户 ID（6 位数字） |
| contact_username | VARCHAR | 20 | NO | - | 联系人姓名 |
| contact_phone | VARCHAR | 20 | NO | - | 联系电话 |
| company_name | VARCHAR | 50 | NO | - | 企业名称 |
| license_number | VARCHAR | 50 | NO | - | 统一社会信用代码 |
| address | VARCHAR | 200 | NO | - | 企业地址 |
| domain | VARCHAR | 200 | NO | - | 企业域名 |
| remark | VARCHAR | 500 | NO | - | 备注 |
| balance | INT | - | NO | 0 | 账户余额（分） |
| package_id | BIGINT | - | NO | NULL | 套餐 ID |
| user_number | INT | - | NO | -1 | 用户数量（-1 不限制） |
| expire_time | DATETIME | - | NO | NULL | 过期时间 |
| account_count | INT | - | NO | 0 | 已注册用户数 |
| status | CHAR | 1 | NO | '0' | 状态（0 正常 1 停用） |
| del_flag | CHAR | 1 | NO | '0' | 删除标志（0 存在 1 删除） |
| create_by | BIGINT | - | NO | NULL | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | BIGINT | - | NO | NULL | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |

**索引：**
- PRIMARY KEY (`id`)
- UNIQUE KEY `uk_tenant_id` (`tenant_id`)
- KEY `idx_company_name` (`company_name`)
- KEY `idx_status` (`status`)
- KEY `idx_del_flag` (`del_flag`)

**约束：**
- `tenant_id` 唯一（6 位数字）
- `company_name` 业务唯一

---

### 1.2 租户套餐表 (sys_tenant_package)

**表名：** `sys_tenant_package`  
**说明：** 存储租户套餐配置信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| id | BIGINT | - | YES | - | 主键 ID |
| name | VARCHAR | 30 | YES | - | 套餐名称 |
| menu_ids | VARCHAR | 2000 | NO | - | 关联菜单 ID 列表（逗号分隔） |
| status | CHAR | 1 | NO | '0' | 状态（0 正常 1 停用） |
| del_flag | CHAR | 1 | NO | '0' | 删除标志（0 存在 1 删除） |
| create_by | BIGINT | - | NO | NULL | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | BIGINT | - | NO | NULL | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |
| remark | VARCHAR | 500 | NO | - | 备注 |

**索引：**
- PRIMARY KEY (`id`)
- UNIQUE KEY `uk_name` (`name`)
- KEY `idx_status` (`status`)
- KEY `idx_del_flag` (`del_flag`)

**约束：**
- `name` 唯一

---

## 二、数据对象类

### 2.1 SysTenant (DO - 数据对象)

```java
@TableName("sys_tenant")
public class SysTenant extends BaseEntity {
    @TableId(value = "id")
    private Long id;
    
    private String tenantId;          // 租户 ID
    private String contactUsername;   // 联系人姓名
    private String contactPhone;      // 联系电话
    private String companyName;       // 企业名称
    private String licenseNumber;     // 统一社会信用代码
    private String address;           // 企业地址
    private String domain;            // 企业域名
    private String remark;            // 备注
    private Integer balance;          // 账户余额
    private Long packageId;           // 套餐 ID
    private Integer userNumber;       // 用户数量限制
    private Date expireTime;          // 过期时间
    private Integer accountCount;     // 已注册用户数
    private String status;            // 状态
    @TableLogic
    private String delFlag;           // 删除标志
}
```

### 2.2 SysTenantBo (BO - 业务对象)

```java
@AutoMapper(target = SysTenant.class, reverseConvertGenerate = false)
public class SysTenantBo extends BaseEntity {
    private Long id;
    
    @NotBlank(message = "企业名称不能为空")
    @Size(min = 2, max = 50, message = "企业名称长度不能超过{max}个字符")
    private String companyName;
    
    @Size(min = 0, max = 20, message = "联系人姓名长度不能超过{max}个字符")
    private String contactUsername;
    
    private String contactPhone;
    
    private Long packageId;
    
    private Integer userNumber;
    
    private Date expireTime;
    
    private String remark;
    
    // 管理员账号信息
    private String username;
    private String password;
    private String nickname;
}
```

### 2.3 SysTenantVo (VO - 视图对象)

```java
@AutoMapper(target = SysTenant.class)
public class SysTenantVo implements Serializable {
    private Long id;
    private String tenantId;
    private String contactUsername;
    private String contactPhone;
    private String companyName;
    private String licenseNumber;
    private String address;
    private String domain;
    private String remark;
    private Integer balance;
    private Long packageId;
    private Integer userNumber;
    private Date expireTime;
    private Integer accountCount;
    private String status;
    private Date createTime;
    
    @Translation(type = TransConstant.PACKAGE_ID_TO_NAME, mapper = "packageId")
    private String packageName;
}
```

### 2.4 SysTenantPackage (DO - 数据对象)

```java
@TableName("sys_tenant_package")
public class SysTenantPackage extends BaseEntity {
    @TableId(value = "id")
    private Long id;
    
    private String name;              // 套餐名称
    private String menuIds;           // 菜单 ID 列表
    private String status;            // 状态
    @TableLogic
    private String delFlag;           // 删除标志
}
```

### 2.5 SysTenantPackageBo (BO - 业务对象)

```java
@AutoMapper(target = SysTenantPackage.class, reverseConvertGenerate = false)
public class SysTenantPackageBo extends BaseEntity {
    private Long id;
    
    @NotBlank(message = "套餐名称不能为空")
    @Size(min = 2, max = 30, message = "套餐名称长度不能超过{max}个字符")
    private String name;
    
    private Long[] menuIds;           // 菜单 ID 数组
    
    private String status;
    
    private String remark;
}
```

### 2.6 SysTenantPackageVo (VO - 视图对象)

```java
@AutoMapper(target = SysTenantPackage.class)
public class SysTenantPackageVo implements Serializable {
    private Long id;
    private String name;
    private String menuIds;
    private String status;
    private String remark;
    private Date createTime;
    
    private List<SysMenuVo> menus;    // 菜单对象列表
}
```

---

## 三、关联实体

### 3.1 菜单实体 (SysMenu)

套餐通过 `menu_ids` 字段关联菜单实体，表示该套餐包含的菜单权限。

**关联字段：**
- `menus`: 菜单对象列表（查询时关联）
- `menuIds`: 菜单 ID 数组（表单提交使用）

### 3.2 用户实体 (SysUser)

租户通过 `tenant_id` 字段与用户建立一对多关系。

**关联字段：**
- `accountCount`: 已注册用户数

---

## 四、数据模型类图

```
┌─────────────────────────┐       ┌──────────────────┐
│      SysTenant          │       │ SysTenantPackage │
├─────────────────────────┤       ├──────────────────┤
│ - id                    │       │ - id             │
│ - tenantId              │       │ - name           │
│ - contactUsername       │       │ - menuIds        │
│ - contactPhone          │       │ - status         │
│ - companyName           │       │ - delFlag        │
│ - licenseNumber         │       │ - remark         │
│ - address               │       └────────┬─────────┘
│ - domain                │                │
│ - remark                │                │ 1:N
│ - balance               │                │
│ - packageId ────────────┴────────────────┘
│ - userNumber            │
│ - expireTime            │       ┌──────────────────┐
│ - accountCount          │       │     SysMenu      │
│ - status                │       ├──────────────────┤
│ - delFlag               │       │ - menuId         │
└───────────┬─────────────┘       │ - menuName       │
            │                     │ - menuType       │
            │ 1:N                 │ - path           │
            │                     └──────────────────┘
            ▼
     ┌──────────────────┐
     │     SysUser      │
     ├──────────────────┤
     │ - userId         │
     │ - tenantId       │
     │ - userName       │
     │ - nickName       │
     └──────────────────┘
```

---

## 五、字段验证规则

### 5.1 SysTenantBo 字段验证

| 字段 | 验证注解 | 规则说明 | 错误提示 |
|------|---------|---------|---------|
| companyName | @NotBlank, @Size(2,50) | 必填，2-50 字符 | 企业名称不能为空/长度不能超过 50 个字符 |
| contactUsername | @Size(0,20) | 可选，最大 20 字符 | 联系人姓名长度不能超过 20 个字符 |
| contactPhone | - | 手机号格式（前端验证） | 请输入正确的手机号码 |
| packageId | - | 套餐 ID 必须存在 | 套餐不存在 |
| userNumber | - | -1 表示不限制 | - |
| expireTime | - | 日期格式 | 请输入正确的过期时间 |

### 5.2 SysTenantPackageBo 字段验证

| 字段 | 验证注解 | 规则说明 | 错误提示 |
|------|---------|---------|---------|
| name | @NotBlank, @Size(2,30) | 必填，2-30 字符，唯一 | 套餐名称不能为空/长度不能超过 30 个字符 |
| menuIds | - | 菜单 ID 数组 | - |
| status | - | 0-正常 / 1-停用 | - |

### 5.3 业务唯一性校验

```java
// 校验企业名称是否唯一
boolean checkCompanyNameUnique(SysTenantBo tenant);

// 校验套餐名称是否唯一
boolean checkNameUnique(SysTenantPackageBo pkg);
```

---

## 六、数据状态说明

### 6.1 租户状态 (status)

| 值 | 说明 | 影响 |
|---|------|------|
| 0 | 正常 | 可正常登录系统 |
| 1 | 停用 | 禁止登录，保留历史数据 |

### 6.2 删除标志 (delFlag)

| 值 | 说明 | 操作 |
|---|------|------|
| 0 | 存在 | 正常数据 |
| 1 | 删除 | 逻辑删除，查询时自动过滤 |

### 6.3 用户数量 (userNumber)

| 值 | 说明 |
|---|------|
| -1 | 不限制用户数量 |
| 正整数 | 最大允许用户数 |

---

## 七、多租户支持

### 7.1 租户 ID 格式

`tenant_id` 为 6 位数字字符串，例如：`000001`, `123456`

| 租户 ID | 说明 |
|--------|------|
| 000000 | 默认租户（超级管理员） |
| 其他 | 自定义租户 |

### 7.2 租户隔离实现

```java
// SysTenant 继承 BaseEntity
public class SysTenant extends BaseEntity {
    // 包含 tenantId 字段
}

// 查询时自动附加租户条件（超管除外）
baseMapper.selectList(wrapper);  // 自动 WHERE tenant_id = ?
```

### 7.3 租户配额检查

```java
// 检查用户数量是否超过限制
if (tenant.getUserNumber() != -1 && tenant.getAccountCount() >= tenant.getUserNumber()) {
    throw new ServiceException("当前租户下用户名额不足，请联系管理员");
}

// 检查是否过期
if (tenant.getExpireTime() != null && new Date().after(tenant.getExpireTime())) {
    throw new ServiceException("租户已过期，请联系管理员续费");
}
```

---

## 八、扩展字段说明

### 8.1 临时字段（非数据库字段）

以下字段用于数据传输和表单处理，不对应数据库字段：

| 字段名 | 类型 | 说明 |
|-------|------|------|
| packageName | String | 套餐名称（查询时关联） |
| menus | List<SysMenuVo> | 菜单对象列表（套餐详情使用） |
| username | String | 管理员账号（创建租户时使用） |
| password | String | 管理员密码（创建租户时使用） |
| nickname | String | 管理员昵称（创建租户时使用） |

---

## 九、数据权限字段

租户表支持基于超级管理员的特殊权限：

- **超级管理员** - 可查看所有租户数据
- **租户管理员** - 仅查看本租户数据

数据范围通过 `TenantHelper` 自动注入 SQL 过滤条件。

