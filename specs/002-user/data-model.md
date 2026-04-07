# 002-用户管理 - 数据模型

**模块编号：** 002  
**模块名称：** 用户管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 用户表 (sys_user)

**表名：** `sys_user`  
**说明：** 存储系统用户基本信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| user_id | BIGINT | - | YES | - | 用户 ID（主键） |
| tenant_id | VARCHAR | 20 | NO | '000000' | 租户编号（Plus 新增） |
| dept_id | BIGINT | - | NO | NULL | 部门 ID |
| user_name | VARCHAR | 30 | YES | - | 用户账号 |
| nick_name | VARCHAR | 30 | YES | - | 用户昵称 |
| user_type | VARCHAR | 10 | NO | 'sys_user' | 用户类型 |
| email | VARCHAR | 50 | NO | - | 用户邮箱 |
| phonenumber | VARCHAR | 11 | NO | - | 手机号码 |
| sex | CHAR | 1 | NO | '0' | 性别（0 男 1 女 2 未知） |
| avatar | BIGINT | - | NO | NULL | 头像地址（OSS ID） |
| password | VARCHAR | 100 | NO | - | 密码（BCrypt 加密） |
| status | CHAR | 1 | NO | '0' | 帐号状态（0 正常 1 停用） |
| del_flag | CHAR | 1 | NO | '0' | 删除标志（0 存在 1 删除） |
| login_ip | VARCHAR | 128 | NO | - | 最后登录 IP |
| login_date | DATETIME | - | NO | NULL | 最后登录时间 |
| create_dept | BIGINT | - | NO | NULL | 创建部门（Plus 新增） |
| create_by | BIGINT | - | NO | NULL | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | BIGINT | - | NO | NULL | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |
| remark | VARCHAR | 500 | NO | - | 备注 |

**索引：**
- PRIMARY KEY (`user_id`)
- KEY `idx_dept_id` (`dept_id`)
- KEY `idx_status` (`status`)
- KEY `idx_del_flag` (`del_flag`)

**约束：**
- `user_name` + `tenant_id` 联合唯一（多租户模式）
- `phonenumber` 业务唯一
- `email` 业务唯一

**与 RuoYi-Vue 的差异：**
| 字段 | RuoYi-Vue | RuoYi-Vue-Plus | 说明 |
|------|-----------|----------------|------|
| tenant_id | 无 | VARCHAR(20) | 多租户支持 |
| create_dept | 无 | BIGINT | 创建部门 |
| create_by/update_by | VARCHAR | BIGINT | 改为用户 ID |
| pwd_update_date | 有 | 无 | 移除 |
| avatar | VARCHAR | BIGINT | 改为 OSS ID |

---

## 二、关联表

### 2.1 用户角色关联表 (sys_user_role)

**表名：** `sys_user_role`  
**说明：** 用户与角色的多对多关联关系

| 字段名 | 类型 | 长度 | 必填 | 说明 |
|-------|------|------|------|------|
| user_id | BIGINT | - | YES | 用户 ID（主键） |
| role_id | BIGINT | - | YES | 角色 ID（主键） |

**索引：**
- PRIMARY KEY (`user_id`, `role_id`)
- KEY `idx_role_id` (`role_id`)

### 2.2 用户岗位关联表 (sys_user_post)

**表名：** `sys_user_post`  
**说明：** 用户与岗位的多对多关联关系

| 字段名 | 类型 | 长度 | 必填 | 说明 |
|-------|------|------|------|------|
| user_id | BIGINT | - | YES | 用户 ID（主键） |
| post_id | BIGINT | - | YES | 岗位 ID（主键） |

**索引：**
- PRIMARY KEY (`user_id`, `post_id`)
- KEY `idx_post_id` (`post_id`)

---

## 三、数据对象类

### 3.1 SysUser (DO - 数据对象)

```java
@TableName("sys_user")
public class SysUser extends TenantEntity {
    @TableId(value = "user_id")
    private Long userId;
    private Long deptId;
    private String userName;
    private String nickName;
    private String userType;
    private String email;
    private String phonenumber;
    private String sex;
    private Long avatar;
    private String password;
    private String status;
    @TableLogic
    private String delFlag;
    private String loginIp;
    private Date loginDate;
    private String remark;
}
```

**说明：**
- 继承 `TenantEntity`，自动支持多租户
- `@TableLogic` 注解实现逻辑删除
- `avatar` 类型为 `Long`，存储 OSS 文件 ID

### 3.2 SysUserBo (BO - 业务对象)

```java
@AutoMapper(target = SysUser.class, reverseConvertGenerate = false)
public class SysUserBo extends BaseEntity {
    private Long userId;
    private Long deptId;
    
    @Xss(message = "用户账号不能包含脚本字符")
    @NotBlank(message = "用户账号不能为空")
    @Size(min = 0, max = 30, message = "用户账号长度不能超过{max}个字符")
    private String userName;
    
    @Xss(message = "用户昵称不能包含脚本字符")
    @NotBlank(message = "用户昵称不能为空")
    @Size(min = 0, max = 30, message = "用户昵称长度不能超过{max}个字符")
    private String nickName;
    
    private String userType;
    
    @Email(message = "邮箱格式不正确")
    @Size(min = 0, max = 50, message = "邮箱长度不能超过{max}个字符")
    private String email;
    
    private String phonenumber;
    private String sex;
    private String password;
    private String status;
    private String remark;
    
    @Size(min = 1, message = "用户角色不能为空")
    private Long[] roleIds;
    private Long[] postIds;
    private Long roleId;
    private String userIds;
    private String excludeUserIds;
}
```

**验证规则：**
- `userName`: 必填，2-30 字符，防 XSS
- `nickName`: 必填，0-30 字符，防 XSS
- `email`: 邮箱格式，最大 50 字符
- `phonenumber`: 手机号格式
- `roleIds`: 新增时必填

### 3.3 SysUserVo (VO - 视图对象)

```java
@AutoMapper(target = SysUser.class)
public class SysUserVo implements Serializable {
    private Long userId;
    private String tenantId;
    private Long deptId;
    private String userName;
    private String nickName;
    private String userType;
    
    @Sensitive(strategy = SensitiveStrategy.EMAIL, perms = "system:user:edit")
    private String email;
    
    @Sensitive(strategy = SensitiveStrategy.PHONE, perms = "system:user:edit")
    private String phonenumber;
    
    private String sex;
    
    @Translation(type = TransConstant.OSS_ID_TO_URL)
    private Long avatar;
    
    @JsonIgnore
    @JsonProperty
    private String password;
    
    private String status;
    private String loginIp;
    private Date loginDate;
    private String remark;
    private Date createTime;
    
    @Translation(type = TransConstant.DEPT_ID_TO_NAME, mapper = "deptId")
    private String deptName;
    
    private List<SysRoleVo> roles;
    private Long[] roleIds;
    private Long[] postIds;
    private Long roleId;
}
```

**特性：**
- `@Sensitive`: 敏感数据脱敏（邮箱、手机号）
- `@Translation`: 自动翻译（部门名称、OSS 头像 URL）
- `@JsonIgnore/@JsonProperty`: 密码字段特殊处理

### 3.4 SysUserInfoVo (用户信息 VO)

```java
public class SysUserInfoVo {
    private SysUserVo user;
    private List<Long> roleIds;
    private List<SysRoleVo> roles;
    private List<Long> postIds;
    private List<SysPostVo> posts;
}
```

**用途：** 用户详情页面，包含完整的用户、角色、岗位信息

### 3.5 UserInfoVo (登录用户信息 VO)

```java
public class UserInfoVo {
    private SysUserVo user;
    private Set<String> permissions;  // 菜单权限
    private Set<String> roles;        // 角色权限
}
```

**用途：** 登录后获取当前用户信息

---

## 四、关联实体

### 4.1 部门实体 (SysDept)

用户表通过 `dept_id` 关联部门实体。

**关联字段：**
- `deptName`: 部门名称（通过 `@Translation` 自动翻译）

### 4.2 角色实体 (SysRole)

用户通过 `sys_user_role` 关联表与角色建立多对多关系。

**关联字段：**
- `roles`: 角色对象列表
- `roleIds`: 角色 ID 数组（表单使用）

### 4.3 岗位实体 (SysPost)

用户通过 `sys_user_post` 关联表与岗位建立多对多关系。

**关联字段：**
- `postIds`: 岗位 ID 数组（表单使用）

---

## 五、字段验证规则

### 5.1 SysUserBo 字段验证

| 字段 | 验证注解 | 规则说明 | 错误提示 |
|------|---------|---------|---------|
| userName | @NotBlank, @Size(0,30), @Xss | 必填，最大 30 字符，防 XSS | 用户账号不能为空/长度不能超过 30 个字符 |
| nickName | @NotBlank, @Size(0,30), @Xss | 必填，最大 30 字符，防 XSS | 用户昵称不能为空/长度不能超过 30 个字符 |
| email | @Email, @Size(0,50) | 邮箱格式，最大 50 字符 | 邮箱格式不正确 |
| phonenumber | - | 手机号格式（前端验证） | 请输入正确的手机号码 |
| password | - | 5-20 字符（前端验证） | 用户密码长度必须介于 5 和 20 之间 |
| roleIds | @Size(min=1) | 新增时必填 | 用户角色不能为空 |

### 5.2 业务唯一性校验

```java
// 校验用户名称是否唯一
boolean checkUserNameUnique(SysUserBo user);

// 校验手机号码是否唯一
boolean checkPhoneUnique(SysUserBo user);

// 校验 email 是否唯一
boolean checkEmailUnique(SysUserBo user);
```

---

## 六、数据状态说明

### 6.1 用户状态 (status)

| 值 | 说明 | 影响 |
|---|------|------|
| 0 | 正常 | 可正常登录系统 |
| 1 | 停用 | 禁止登录，保留历史数据 |

### 6.2 删除标志 (delFlag)

| 值 | 说明 | 操作 |
|---|------|------|
| 0 | 存在 | 正常数据 |
| 1 | 删除 | 逻辑删除，查询时自动过滤 |

### 6.3 性别 (sex)

| 值 | 说明 | 字典类型 |
|---|------|---------|
| 0 | 男 | sys_user_sex |
| 1 | 女 | sys_user_sex |
| 2 | 未知 | sys_user_sex |

### 6.4 用户类型 (userType)

| 值 | 说明 |
|---|------|
| sys_user | 系统用户（默认） |

---

## 七、扩展字段说明

### 7.1 临时字段（非数据库字段）

以下字段用于数据传输和表单处理，不对应数据库字段：

| 字段名 | 类型 | 说明 |
|-------|------|------|
| deptName | String | 部门名称（查询时关联） |
| roles | List<SysRoleVo> | 角色对象列表（查询时关联） |
| roleIds | Long[] | 角色 ID 数组（表单提交使用） |
| postIds | Long[] | 岗位 ID 数组（表单提交使用） |
| roleId | Long | 单个角色 ID（特殊场景使用） |
| userIds | String | 用户 ID 串（批量操作用） |
| excludeUserIds | String | 排除的用户 ID（工作流用） |

---

## 八、数据权限字段

用户表支持数据权限过滤，通过以下字段实现：

- `dept_id` - 部门 ID：用于部门数据范围过滤
- `create_by` - 创建者：用于仅本人数据权限过滤

数据范围通过 `DataPermissionHelper` 自动注入 SQL 过滤条件。

---

## 九、多租户支持

### 9.1 租户字段

`sys_user` 表包含 `tenant_id` 字段，用于多租户隔离。

| 租户 ID | 说明 |
|--------|------|
| 000000 | 默认租户（演示租户） |
| 其他 | 自定义租户 |

### 9.2 租户隔离实现

```java
// SysUser 继承 TenantEntity
public class SysUser extends TenantEntity {
    // 自动包含 tenantId 字段
}

// 查询时自动附加租户条件
baseMapper.selectList(wrapper);  // 自动 WHERE tenant_id = ?
```

### 9.3 租户配额检查

```java
if (TenantHelper.isEnable()) {
    if (!tenantService.checkAccountBalance(TenantHelper.getTenantId())) {
        return R.fail("当前租户下用户名额不足，请联系管理员");
    }
}
```

