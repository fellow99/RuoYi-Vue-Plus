# 整体数据模型 (overall-data-model.md)

**版本：** 5.5.3  
**最后更新：** 2026-03-13  
**项目：** RuoYi-Vue-Plus

---

## 一、核心数据实体

### 1.1 实体分类

系统核心数据实体分为以下几类：

| 分类 | 实体 | 说明 |
|------|------|------|
| 用户相关 | SysUser | 用户信息 |
| 角色相关 | SysRole | 角色信息 |
| 菜单相关 | SysMenu | 菜单权限 |
| 部门相关 | SysDept | 部门信息 |
| 岗位相关 | SysPost | 岗位信息 |
| 字典相关 | SysDictType, SysDictData | 字典类型、字典数据 |
| 参数相关 | SysConfig | 参数配置 |
| 日志相关 | SysOperLog, SysLogininfor | 操作日志、登录日志 |
| 通知相关 | SysNotice | 通知公告 |
| 租户相关 | SysTenant, SysTenantPackage | 租户、租户套餐 |
| 客户端相关 | SysClient | 客户端配置 |
| 文件相关 | SysOss, SysOssConfig | 文件信息、文件配置 |

---

## 二、实体关系

### 2.1 实体关系图 (ERD)

```
┌─────────────┐       ┌─────────────┐       ┌─────────────┐
│  SysUser    │       │  SysRole    │       │  SysMenu    │
│  (用户)      │       │  (角色)      │       │  (菜单)      │
└──────┬──────┘       └──────┬──────┘       └──────┬──────┘
       │                    │                    │
       │                    │                    │
       ▼                    ▼                    ▼
┌─────────────┐       ┌─────────────┐       ┌─────────────┐
│  SysDept    │       │  SysPost    │       │  SysDict    │
│  (部门)      │       │  (岗位)      │       │  (字典)      │
└─────────────┘       └─────────────┘       └─────────────┘
       │
       ▼
┌─────────────┐
│ SysTenant   │
│  (租户)      │
└─────────────┘
```

### 2.2 用户关系

```
SysUser (用户)
    │
    ├── belongs_to → SysDept (部门)
    │                 多对一，一个用户属于一个部门
    │
    ├── belongs_to → SysPost (岗位)
    │                 多对多，一个用户可担任多个岗位
    │                 关联表：sys_user_post
    │
    ├── belongs_to → SysRole (角色)
    │                 多对多，一个用户可拥有多个角色
    │                 关联表：sys_user_role
    │
    └── belongs_to → SysTenant (租户)
                      多对一，一个用户属于一个租户
```

### 2.3 角色关系

```
SysRole (角色)
    │
    ├── has_many → SysUser (用户)
    │              多对多，一个角色可分配给多个用户
    │              关联表：sys_user_role
    │
    ├── has_many → SysMenu (菜单)
    │              多对多，一个角色可拥有多个菜单权限
    │              关联表：sys_role_menu
    │
    └── has_many → SysDept (部门)
                   多对多，角色可访问多个部门数据
                   关联表：sys_role_dept
```

### 2.4 租户关系

```
SysTenant (租户)
    │
    ├── has_many → SysUser (用户)
    │              一对多，一个租户有多个用户
    │
    ├── belongs_to → SysTenantPackage (套餐)
    │                 多对一，一个租户使用一个套餐
    │
    └── has_many → SysClient (客户端)
                   一对多，一个租户可配置多个客户端
```

---

## 三、核心实体详情

### 3.1 SysUser (用户表)

**表名：** sys_user

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| user_id | bigint | 用户 ID (雪花 ID) | 是 |
| tenant_id | varchar | 租户 ID | 是 |
| dept_id | bigint | 部门 ID | 否 |
| user_name | varchar | 用户账号 | 是 |
| nick_name | varchar | 用户昵称 | 是 |
| user_type | varchar | 用户类型 (sys_user 系统用户) | 否 |
| email | varchar | 邮箱 | 否 |
| phonenumber | varchar | 手机号码 | 否 |
| sex | char | 性别 (0 男 1 女 2 未知) | 否 |
| avatar | bigint | 头像地址 (文件 ID) | 否 |
| password | varchar | 密码 (BCrypt 加密) | 是 |
| status | char | 帐号状态 (0 正常 1 停用) | 是 |
| del_flag | char | 删除标志 (0 正常 1 删除) | 是 |
| login_ip | varchar | 最后登录 IP | 否 |
| login_date | datetime | 最后登录时间 | 否 |
| create_by | varchar | 创建者 | 否 |
| create_time | datetime | 创建时间 | 是 |
| update_by | varchar | 更新者 | 否 |
| update_time | datetime | 更新时间 | 是 |
| remark | varchar | 备注 | 否 |

**索引：**
- PRIMARY KEY (user_id)
- UNIQUE (user_name)
- INDEX (phonenumber)
- INDEX (dept_id)
- INDEX (tenant_id)

### 3.2 SysRole (角色表)

**表名：** sys_role

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| role_id | bigint | 角色 ID | 是 |
| tenant_id | varchar | 租户 ID | 是 |
| role_name | varchar | 角色名称 | 是 |
| role_key | varchar | 角色权限字符串 | 是 |
| role_sort | int | 显示顺序 | 是 |
| data_scope | char | 数据范围 (1 全部 2 自定义 3 本部门 4 本部门及以下 5 仅本人) | 是 |
| menu_check_strictly | char | 菜单树选择项是否关联显示 | 是 |
| dept_check_strictly | char | 部门树选择项是否关联显示 | 是 |
| status | char | 角色状态 (0 正常 1 停用) | 是 |
| del_flag | char | 删除标志 | 是 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |
| remark | varchar | 备注 | 否 |

**索引：**
- PRIMARY KEY (role_id)
- UNIQUE (role_key)
- INDEX (tenant_id)

### 3.3 SysMenu (菜单表)

**表名：** sys_menu

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| menu_id | bigint | 菜单 ID | 是 |
| menu_name | varchar | 菜单名称 | 是 |
| parent_id | bigint | 父菜单 ID | 是 |
| order_num | int | 显示顺序 | 是 |
| path | varchar | 路由地址 | 否 |
| component | varchar | 组件路径 | 否 |
| query_param | varchar | 路由参数 | 否 |
| is_frame | char | 是否为外链 (0 是 1 否) | 是 |
| is_cache | char | 是否缓存 (0 缓存 1 不缓存) | 是 |
| menu_type | char | 菜单类型 (M 目录 C 菜单 F 按钮) | 是 |
| visible | char | 显示状态 (0 显示 1 隐藏) | 是 |
| status | char | 菜单状态 (0 正常 1 停用) | 是 |
| perms | varchar | 权限标识 | 否 |
| icon | varchar | 菜单图标 | 否 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |
| remark | varchar | 备注 | 否 |

**索引：**
- PRIMARY KEY (menu_id)
- INDEX (parent_id)

### 3.4 SysDept (部门表)

**表名：** sys_dept

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| dept_id | bigint | 部门 ID | 是 |
| tenant_id | varchar | 租户 ID | 是 |
| parent_id | bigint | 父部门 ID | 是 |
| ancestors | varchar | 祖级列表 | 是 |
| dept_name | varchar | 部门名称 | 是 |
| dept_category | varchar | 部门类别编码 | 否 |
| order_num | int | 显示顺序 | 是 |
| leader | bigint | 负责人 (用户 ID) | 否 |
| phone | varchar | 联系电话 | 否 |
| email | varchar | 邮箱 | 否 |
| status | char | 部门状态 (0 正常 1 停用) | 是 |
| del_flag | char | 删除标志 | 是 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |

**索引：**
- PRIMARY KEY (dept_id)
- INDEX (parent_id)
- INDEX (ancestors)
- INDEX (tenant_id)

### 3.5 SysPost (岗位表)

**表名：** sys_post

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| post_id | bigint | 岗位序号 | 是 |
| tenant_id | varchar | 租户 ID | 是 |
| dept_id | bigint | 部门 ID | 否 |
| post_code | varchar | 岗位编码 | 是 |
| post_name | varchar | 岗位名称 | 是 |
| post_category | varchar | 岗位类别编码 | 否 |
| post_sort | int | 岗位排序 | 是 |
| status | char | 状态 (0 正常 1 停用) | 是 |
| remark | varchar | 备注 | 否 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |

**索引：**
- PRIMARY KEY (post_id)
- INDEX (tenant_id)

### 3.6 SysDictType (字典类型表)

**表名：** sys_dict_type

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| dict_id | bigint | 字典主键 | 是 |
| tenant_id | varchar | 租户 ID | 是 |
| dict_name | varchar | 字典名称 | 是 |
| dict_type | varchar | 字典类型 | 是 |
| remark | varchar | 备注 | 否 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |

**索引：**
- PRIMARY KEY (dict_id)
- UNIQUE (dict_type)
- INDEX (tenant_id)

### 3.7 SysDictData (字典数据表)

**表名：** sys_dict_data

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| dict_code | bigint | 字典编码 | 是 |
| tenant_id | varchar | 租户 ID | 是 |
| dict_sort | int | 字典排序 | 是 |
| dict_label | varchar | 字典标签 | 是 |
| dict_value | varchar | 字典键值 | 是 |
| dict_type | varchar | 字典类型 | 是 |
| css_class | varchar | 样式属性 | 否 |
| list_class | varchar | 表格回显样式 | 否 |
| is_default | char | 是否默认 (Y 是 N 否) | 否 |
| status | char | 状态 (0 正常 1 停用) | 是 |
| remark | varchar | 备注 | 否 |

**索引：**
- PRIMARY KEY (dict_code)
- INDEX (dict_type)
- INDEX (tenant_id)

### 3.8 SysConfig (参数配置表)

**表名：** sys_config

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| config_id | bigint | 参数主键 | 是 |
| tenant_id | varchar | 租户 ID | 是 |
| config_name | varchar | 参数名称 | 是 |
| config_key | varchar | 参数键名 | 是 |
| config_value | varchar | 参数键值 | 是 |
| config_type | char | 系统内置 (Y 是 N 否) | 是 |
| remark | varchar | 备注 | 否 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |

**索引：**
- PRIMARY KEY (config_id)
- UNIQUE (config_key)
- INDEX (tenant_id)

### 3.9 SysNotice (通知公告表)

**表名：** sys_notice

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| notice_id | bigint | 公告 ID | 是 |
| tenant_id | varchar | 租户 ID | 是 |
| notice_title | varchar | 公告标题 | 是 |
| notice_type | char | 公告类型 (1 通知 2 公告) | 是 |
| notice_content | text | 公告内容 | 是 |
| status | char | 公告状态 (0 正常 1 关闭) | 是 |
| remark | varchar | 备注 | 否 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |

**索引：**
- PRIMARY KEY (notice_id)
- INDEX (tenant_id)

### 3.10 SysOperLog (操作日志表)

**表名：** sys_oper_log

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| oper_id | bigint | 日志主键 | 是 |
| tenant_id | varchar | 租户编号 | 是 |
| title | varchar | 操作模块 | 否 |
| business_type | int | 业务类型 | 否 |
| method | varchar | 请求方法 | 否 |
| request_method | varchar | 请求方式 | 否 |
| operator_type | int | 操作类别 | 否 |
| oper_name | varchar | 操作人员 | 否 |
| dept_name | varchar | 部门名称 | 否 |
| oper_url | varchar | 请求 url | 否 |
| oper_ip | varchar | 操作地址 | 否 |
| oper_location | varchar | 操作地点 | 否 |
| oper_param | varchar | 请求参数 | 否 |
| json_result | varchar | 返回参数 | 否 |
| status | int | 操作状态 | 否 |
| errorMsg | varchar | 错误消息 | 否 |
| oper_time | datetime | 操作时间 | 否 |
| cost_time | bigint | 消耗时间 | 否 |

**索引：**
- PRIMARY KEY (oper_id)
- INDEX (tenant_id)
- INDEX (oper_time)

### 3.11 SysLogininfor (登录日志表)

**表名：** sys_logininfor

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| info_id | bigint | ID | 是 |
| tenant_id | varchar | 租户编号 | 是 |
| user_name | varchar | 用户账号 | 否 |
| client_key | varchar | 客户端 | 否 |
| device_type | varchar | 设备类型 | 否 |
| status | char | 登录状态 (0 成功 1 失败) | 否 |
| ipaddr | varchar | 登录 IP 地址 | 否 |
| login_location | varchar | 登录地点 | 否 |
| browser | varchar | 浏览器类型 | 否 |
| os | varchar | 操作系统 | 否 |
| msg | varchar | 提示消息 | 否 |
| login_time | datetime | 访问时间 | 否 |

**索引：**
- PRIMARY KEY (info_id)
- INDEX (tenant_id)
- INDEX (login_time)

### 3.12 SysTenant (租户表)

**表名：** sys_tenant

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| id | bigint | 主键 ID | 是 |
| tenant_id | varchar | 租户编号 | 是 |
| contact_user_name | varchar | 联系人 | 否 |
| contact_phone | varchar | 联系电话 | 否 |
| company_name | varchar | 企业名称 | 否 |
| license_number | varchar | 统一社会信用代码 | 否 |
| address | varchar | 地址 | 否 |
| domain | varchar | 域名 | 否 |
| intro | text | 企业简介 | 否 |
| remark | varchar | 备注 | 否 |
| package_id | bigint | 租户套餐编号 | 否 |
| expire_time | datetime | 过期时间 | 否 |
| account_count | bigint | 用户数量 (-1 不限制) | 否 |
| status | char | 租户状态 (0 正常 1 停用) | 是 |
| del_flag | char | 删除标志 | 是 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |

**索引：**
- PRIMARY KEY (id)
- UNIQUE (tenant_id)

### 3.13 SysTenantPackage (租户套餐表)

**表名：** sys_tenant_package

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| package_id | bigint | 租户套餐 ID | 是 |
| package_name | varchar | 套餐名称 | 是 |
| menu_ids | varchar | 关联菜单 ID 集合 (逗号分隔) | 否 |
| menu_check_strictly | char | 菜单树选择项是否关联显示 | 是 |
| status | char | 状态 (0 正常 1 停用) | 是 |
| del_flag | char | 删除标志 | 是 |
| remark | varchar | 备注 | 否 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |

**索引：**
- PRIMARY KEY (package_id)

### 3.14 SysClient (客户端表)

**表名：** sys_client

| 字段 | 类型 | 说明 | 必填 |
|------|------|------|------|
| client_id | bigint | 客户端 ID | 是 |
| tenant_id | varchar | 租户 ID | 是 |
| client_key | varchar | 客户端标识 | 是 |
| client_name | varchar | 客户端名称 | 是 |
| client_type | varchar | 客户端类型 (pc/app/wx) | 是 |
| grant_type | varchar | 授权类型 | 是 |
| login_mode | varchar | 登录模式 (password/sms/code) | 是 |
| token_timeout | int | Token 超时时间 (秒) | 是 |
| status | char | 状态 (0 正常 1 停用) | 是 |
| del_flag | char | 删除标志 | 是 |
| create_time | datetime | 创建时间 | 是 |
| update_time | datetime | 更新时间 | 是 |

**索引：**
- PRIMARY KEY (client_id)
- INDEX (tenant_id)
- INDEX (client_key)

---

## 四、关联表详情

### 4.1 sys_user_role (用户角色关联表)

| 字段 | 类型 | 说明 |
|------|------|------|
| user_id | bigint | 用户 ID |
| role_id | bigint | 角色 ID |

**索引：** PRIMARY KEY (user_id, role_id)

### 4.2 sys_role_menu (角色菜单关联表)

| 字段 | 类型 | 说明 |
|------|------|------|
| role_id | bigint | 角色 ID |
| menu_id | bigint | 菜单 ID |

**索引：** PRIMARY KEY (role_id, menu_id)

### 4.3 sys_role_dept (角色部门关联表)

| 字段 | 类型 | 说明 |
|------|------|------|
| role_id | bigint | 角色 ID |
| dept_id | bigint | 部门 ID |

**索引：** PRIMARY KEY (role_id, dept_id)

### 4.4 sys_user_post (用户岗位关联表)

| 字段 | 类型 | 说明 |
|------|------|------|
| user_id | bigint | 用户 ID |
| post_id | bigint | 岗位 ID |

**索引：** PRIMARY KEY (user_id, post_id)

---

## 五、数据权限设计

### 5.1 数据范围

| 范围 | 说明 | SQL 过滤条件 |
|------|------|-------------|
| 全部数据 | 查看所有数据 | 无过滤 |
| 自定义数据 | 按角色自定义数据范围 | `dept_id IN (custom_dept_ids)` |
| 本部门及以下 | 查看本部门及下级部门 | `dept_id IN (dept_ancestors)` |
| 本部门 | 仅查看本部门 | `dept_id = current_dept_id` |
| 仅本人 | 仅查看本人数据 | `user_id = current_user_id` |

### 5.2 租户隔离

所有业务表必须包含 `tenant_id` 字段，通过 MyBatis-Plus 多租户插件自动注入过滤条件：

```sql
SELECT * FROM sys_user WHERE tenant_id = {current_tenant_id}
```

---

## 六、审计字段设计

### 6.1  BaseEntity 基类字段

所有继承 BaseEntity 的实体自动包含以下字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| create_time | datetime | 创建时间 |
| update_time | datetime | 更新时间 |

### 6.2 TenantEntity 基类字段

所有继承 TenantEntity 的实体自动包含以下字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| tenant_id | varchar | 租户 ID |
| create_time | datetime | 创建时间 |
| update_time | datetime | 更新时间 |

### 6.3 软删除字段

| 字段 | 类型 | 说明 |
|------|------|------|
| del_flag | char | 删除标志 (0 正常 1 删除) |

---

## 七、数据库规范

### 7.1 基本规范

| 规范项 | 要求 |
|--------|------|
| 字符集 | utf8mb4 |
| 排序规则 | utf8mb4_general_ci |
| 引擎 | InnoDB |
| 主键 | 雪花 ID (bigint) |
| 时间字段 | datetime |
| 软删除 | del_flag (char) |

### 7.2 命名规范

| 类型 | 规范 | 示例 |
|------|------|------|
| 表名 | sys_前缀，小写，下划线分隔 | sys_user |
| 字段名 | 小写，下划线分隔 | user_name |
| 主键 | 表名_单数形式 + _id | user_id |
| 外键 | 关联表名_单数形式 + _id | dept_id |
| 索引 | idx_字段名 | idx_user_name |
| 唯一索引 | uk_字段名 | uk_user_name |

---

**文档结束**
