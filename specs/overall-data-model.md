# 整体数据模型 (overall-data-model.md)

**版本：** 6.0.0  
**最后更新：** 2026-08-10  
**项目：** RuoYi-Vue-Plus

---

## 一、核心数据实体

### 1.1 实体分类

| 分类 | 实体 | 数据库表 | 说明 |
|------|------|----------|------|
| 用户相关 | SysUser | sys_user | 用户基本信息 |
| 角色相关 | SysRole | sys_role | 角色定义 |
| 菜单相关 | SysMenu | sys_menu | 菜单/按钮权限 |
| 部门相关 | SysDept | sys_dept | 组织架构 |
| 岗位相关 | SysPost | sys_post | 用户岗位 |
| 字典相关 | SysDictType, SysDictData | sys_dict_type, sys_dict_data | 系统字典 |
| 参数相关 | SysConfig | sys_config | 系统参数 |
| 日志相关 | SysOperLog, SysLoginInfo | sys_oper_log, sys_login_info | 操作/登录日志 |
| 通知相关 | SysNotice | sys_notice | 通知公告 |
| 文件相关 | SysOss, SysOssConfig | sys_oss, sys_oss_config | 文件存储与配置 |
| 客户端相关 | SysClient | sys_client | 多客户端管理 |
| 消息相关 | SysMessage | sys_message | 消息中心 |
| 社交相关 | SysSocial | sys_social | 第三方登录 |
| 关联表 | SysUserRole, SysUserPost, SysRoleMenu, SysRoleDept | sys_user_role 等 | 多对多关联 |

---

## 二、核心实体关系

### 2.1 用户关系

```
SysUser (sys_user)
    │
    ├── 属于 → SysDept (多对一, dept_id)
    ├── 拥有 → SysPost (多对多, 关联表 sys_user_post)
    ├── 拥有 → SysRole (多对多, 关联表 sys_user_role)
    └── 绑定 → SysSocial (一对多, user_id)
```

### 2.2 角色关系

```
SysRole (sys_role)
    │
    ├── 分配 → SysUser (多对多, 关联表 sys_user_role)
    ├── 拥有 → SysMenu (多对多, 关联表 sys_role_menu)
    └── 可见 → SysDept (多对多, 关联表 sys_role_dept, 数据权限)
```

### 2.3 菜单关系

```
SysMenu (sys_menu) ─── 自引用树形结构 (parent_id)
```

### 2.4 部门关系

```
SysDept (sys_dept) ─── 自引用树形结构 (parent_id, ancestors)
```

### 2.5 字典关系

```
SysDictType (sys_dict_type) ─── SysDictData (sys_dict_data)
    1 : N (通过 dict_type 字段关联)
```

### 2.6 文件关系

```
SysOssConfig (sys_oss_config, 存储配置)
    1 : N
SysOss (sys_oss, 文件记录, 内嵌 SysOssExt JSON)
```

---

## 三、通用审计字段（BaseEntity）

所有核心实体（除日志实体和关联表外）继承 `BaseEntity`：

| 字段 | 类型 | 说明 |
|------|------|------|
| createDept | Long | 创建部门 |
| createBy | Long | 创建人 |
| createTime | LocalDateTime | 创建时间 |
| updateBy | Long | 更新人 |
| updateTime | LocalDateTime | 更新时间 |

逻辑删除标记 `delFlag`（String, `@TableLogic`）：只存在于需要逻辑删除的实体中。

---

## 四、核心实体字段摘要

### 4.1 SysUser（用户）

| 字段 | 类型 | 说明 |
|------|------|------|
| userId | Long | 主键（雪花 ID） |
| deptId | Long | 所属部门 |
| userName | String | 登录账号（唯一） |
| nickName | String | 用户昵称 |
| userType | String | 用户类型 |
| email | String | 邮箱 |
| phoneNumber | String | 手机号 |
| gender | String | 性别 |
| avatar | Long | 头像（OSS ID） |
| password | String | 密码（BCrypt） |
| status | String | 状态（0正常/1停用） |

### 4.2 SysRole（角色）

| 字段 | 类型 | 说明 |
|------|------|------|
| roleId | Long | 主键 |
| roleName | String | 角色名称 |
| roleKey | String | 权限标识（如 admin） |
| roleSort | Integer | 排序 |
| dataScope | String | 数据范围 |
| status | String | 状态 |

### 4.3 SysMenu（菜单）

| 字段 | 类型 | 说明 |
|------|------|------|
| menuId | Long | 主键 |
| parentId | Long | 父菜单 ID（0=顶级） |
| menuName | String | 菜单名称 |
| menuType | String | 类型（M目录/C菜单/F按钮） |
| path | String | 路由地址 |
| component | String | 组件路径 |
| perms | String | 权限标识 |
| icon | String | 图标 |

### 4.4 SysDept（部门）

| 字段 | 类型 | 说明 |
|------|------|------|
| deptId | Long | 主键 |
| parentId | Long | 父部门 ID |
| ancestors | String | 祖级列表（如 0,100,200） |
| deptName | String | 部门名称 |
| orderNum | Integer | 排序 |
| leader | Long | 负责人（用户 ID） |
| status | String | 状态 |

### 4.5 SysDictData（字典数据）

| 字段 | 类型 | 说明 |
|------|------|------|
| dictCode | Long | 主键 |
| dictSort | Integer | 排序 |
| dictLabel | String | 显示标签 |
| dictValue | String | 字典值 |
| dictType | String | 字典类型（关联 SysDictType） |
| isDefault | String | 是否默认 |

---

## 五、日志实体（独立结构）

### 5.1 SysOperLog（操作日志）

无 BaseEntity，实现 Serializable。关键字段：operId, title, businessType, method, requestMethod, operatorType, operName, operUrl, operIp, operLocation, operParam, jsonResult, status, errorMsg, costTime。

### 5.2 SysLoginInfo（登录日志）

无 BaseEntity，实现 Serializable。关键字段：infoId, userName, clientKey, deviceType, status, ipaddr, loginLocation, browser, os, msg, loginTime。

---

## 六、在线用户（Redis 缓存）

`SysUserOnline` 为瞬态视图对象，非数据库表，数据存储在 Redis 中（key: `CacheNames.ONLINE_TOKEN_KEY + token`）。包含字段：tokenId, deptName, userName, clientKey, deviceType, ipaddr, loginLocation, browser, os, loginTime。
