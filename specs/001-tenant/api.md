# 001-租户管理 - API 清单

**模块编号：** 001  
**模块名称：** 租户管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、租户管理接口

### 1.1 查询租户列表

**接口地址：** `GET /system/tenant/list`

**权限标识：** `system:tenant:list`  
**角色要求：** `superadmin`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| tenantId | String | 否 | 租户 ID |
| companyName | String | 否 | 企业名称（模糊） |
| contactUsername | String | 否 | 联系人姓名（模糊） |
| contactPhone | String | 否 | 联系电话（模糊） |
| status | String | 否 | 状态（0-正常 / 1-停用） |
| pageNum | Integer | 否 | 页码，默认 1 |
| pageSize | Integer | 否 | 每页数量，默认 10 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "rows": [
    {
      "id": 1,
      "tenantId": "000001",
      "companyName": "示例企业",
      "contactUsername": "张三",
      "contactPhone": "13800138000",
      "packageName": "基础版",
      "userNumber": 100,
      "accountCount": 25,
      "expireTime": "2026-12-31 23:59:59",
      "status": "0",
      "createTime": "2026-01-01 00:00:00"
    }
  ],
  "total": 1
}
```

---

### 1.2 获取租户详情

**接口地址：** `GET /system/tenant/{id}`

**权限标识：** `system:tenant:query`  
**角色要求：** `superadmin`

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| id | Long | 是 | 租户主键 ID |

**响应参数：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "id": 1,
    "tenantId": "000001",
    "companyName": "示例企业",
    "contactUsername": "张三",
    "contactPhone": "13800138000",
    "licenseNumber": "91310000XXXXXXXXXX",
    "address": "上海市浦东新区",
    "domain": "https://example.com",
    "remark": "备注信息",
    "balance": 10000,
    "packageId": 1,
    "packageName": "基础版",
    "userNumber": 100,
    "accountCount": 25,
    "expireTime": "2026-12-31 23:59:59",
    "status": "0",
    "createTime": "2026-01-01 00:00:00"
  }
}
```

---

### 1.3 新增租户

**接口地址：** `POST /system/tenant`

**权限标识：** `system:tenant:add`  
**角色要求：** `superadmin`

**请求参数：**

```json
{
  "companyName": "示例企业",
  "contactUsername": "张三",
  "contactPhone": "13800138000",
  "licenseNumber": "91310000XXXXXXXXXX",
  "address": "上海市浦东新区",
  "domain": "https://example.com",
  "packageId": 1,
  "userNumber": 100,
  "expireTime": "2026-12-31 23:59:59",
  "remark": "备注信息",
  "username": "admin",
  "password": "admin123",
  "nickname": "管理员"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| companyName | String | 是 | 企业名称（2-50 字符） |
| contactUsername | String | 否 | 联系人姓名 |
| contactPhone | String | 否 | 联系电话 |
| licenseNumber | String | 否 | 统一社会信用代码 |
| address | String | 否 | 企业地址 |
| domain | String | 否 | 企业域名 |
| packageId | Long | 是 | 套餐 ID |
| userNumber | Integer | 否 | 用户数量限制（-1 不限制） |
| expireTime | String | 否 | 过期时间（ISO 格式） |
| remark | String | 否 | 备注 |
| username | String | 是 | 管理员账号 |
| password | String | 是 | 管理员密码（加密传输） |
| nickname | String | 否 | 管理员昵称 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "新增成功"
}
```

---

### 1.4 修改租户

**接口地址：** `PUT /system/tenant`

**权限标识：** `system:tenant:edit`  
**角色要求：** `superadmin`

**请求参数：**

```json
{
  "id": 1,
  "tenantId": "000001",
  "companyName": "示例企业",
  "contactUsername": "李四",
  "contactPhone": "13900139000",
  "packageId": 2,
  "userNumber": 200,
  "expireTime": "2027-12-31 23:59:59",
  "remark": "更新备注"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| id | Long | 是 | 租户主键 ID |
| tenantId | String | 是 | 租户 ID（不可修改） |
| companyName | String | 是 | 企业名称（不可修改） |
| contactUsername | String | 否 | 联系人姓名 |
| contactPhone | String | 否 | 联系电话 |
| packageId | Long | 否 | 套餐 ID |
| userNumber | Integer | 否 | 用户数量限制 |
| expireTime | String | 否 | 过期时间 |
| remark | String | 否 | 备注 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "修改成功"
}
```

---

### 1.5 修改租户状态

**接口地址：** `PUT /system/tenant/changeStatus`

**权限标识：** `system:tenant:edit`  
**角色要求：** `superadmin`

**请求参数：**

```json
{
  "id": 1,
  "tenantId": "000001",
  "status": "1"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| id | Long | 是 | 租户主键 ID |
| tenantId | String | 是 | 租户 ID |
| status | String | 是 | 状态（0-正常 / 1-停用） |

**响应参数：**

```json
{
  "code": 200,
  "msg": "修改成功"
}
```

---

### 1.6 删除租户

**接口地址：** `DELETE /system/tenant/{ids}`

**权限标识：** `system:tenant:remove`  
**角色要求：** `superadmin`

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| ids | Long[] | 是 | 租户主键 ID 数组 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "删除成功"
}
```

---

### 1.7 导出租户

**接口地址：** `POST /system/tenant/export`

**权限标识：** `system:tenant:export`  
**角色要求：** `superadmin`

**请求参数：** 同查询列表

**响应：** Excel 文件

---

### 1.8 动态切换租户

**接口地址：** `GET /system/tenant/dynamic/{tenantId}`

**权限标识：** 无（仅超级管理员）  
**角色要求：** `superadmin`

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| tenantId | String | 是 | 目标租户 ID |

**响应参数：**

```json
{
  "code": 200,
  "msg": "切换成功"
}
```

---

### 1.9 清除动态租户

**接口地址：** `GET /system/tenant/dynamic/clear`

**权限标识：** 无（仅超级管理员）  
**角色要求：** `superadmin`

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

---

### 1.10 同步租户套餐

**接口地址：** `GET /system/tenant/syncTenantPackage`

**权限标识：** `system:tenant:edit`  
**角色要求：** `superadmin`

**响应参数：**

```json
{
  "code": 200,
  "msg": "同步成功"
}
```

---

### 1.11 同步租户字典

**接口地址：** `GET /system/tenant/syncTenantDict`

**权限标识：** 无（仅超级管理员）  
**角色要求：** `superadmin`

**响应参数：**

```json
{
  "code": 200,
  "msg": "同步成功"
}
```

---

### 1.12 同步租户参数

**接口地址：** `GET /system/tenant/syncTenantConfig`

**权限标识：** 无（仅超级管理员）  
**角色要求：** `superadmin`

**响应参数：**

```json
{
  "code": 200,
  "msg": "同步成功"
}
```

---

## 二、租户套餐管理接口

### 2.1 查询套餐列表

**接口地址：** `GET /system/tenant/package/list`

**权限标识：** `system:tenantPackage:list`  
**角色要求：** `superadmin`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| name | String | 否 | 套餐名称（模糊） |
| status | String | 否 | 状态（0-正常 / 1-停用） |
| pageNum | Integer | 否 | 页码，默认 1 |
| pageSize | Integer | 否 | 每页数量，默认 10 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "rows": [
    {
      "id": 1,
      "name": "基础版",
      "menuIds": "1,2,3,4,5",
      "status": "0",
      "remark": "基础功能套餐",
      "createTime": "2026-01-01 00:00:00"
    }
  ],
  "total": 1
}
```

---

### 2.2 获取套餐详情

**接口地址：** `GET /system/tenant/package/{packageId}`

**权限标识：** `system:tenantPackage:query`  
**角色要求：** `superadmin`

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| packageId | Long | 是 | 套餐主键 ID |

**响应参数：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "id": 1,
    "name": "基础版",
    "menuIds": "1,2,3,4,5",
    "menus": [
      {"menuId": 1, "menuName": "系统管理"},
      {"menuId": 2, "menuName": "用户管理"}
    ],
    "status": "0",
    "remark": "基础功能套餐",
    "createTime": "2026-01-01 00:00:00"
  }
}
```

---

### 2.3 新增套餐

**接口地址：** `POST /system/tenant/package`

**权限标识：** `system:tenantPackage:add`  
**角色要求：** `superadmin`

**请求参数：**

```json
{
  "name": "高级版",
  "menuIds": [1, 2, 3, 4, 5, 6, 7],
  "status": "0",
  "remark": "高级功能套餐"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| name | String | 是 | 套餐名称（2-30 字符） |
| menuIds | Long[] | 是 | 菜单 ID 数组 |
| status | String | 否 | 状态（0-正常 / 1-停用），默认 0 |
| remark | String | 否 | 备注 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "新增成功"
}
```

---

### 2.4 修改套餐

**接口地址：** `PUT /system/tenant/package`

**权限标识：** `system:tenantPackage:edit`  
**角色要求：** `superadmin`

**请求参数：**

```json
{
  "id": 1,
  "name": "高级版",
  "menuIds": [1, 2, 3, 4, 5, 6, 7, 8],
  "status": "0",
  "remark": "更新备注"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| id | Long | 是 | 套餐主键 ID |
| name | String | 是 | 套餐名称 |
| menuIds | Long[] | 是 | 菜单 ID 数组 |
| status | String | 否 | 状态 |
| remark | String | 否 | 备注 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "修改成功"
}
```

---

### 2.5 修改套餐状态

**接口地址：** `PUT /system/tenant/package/changeStatus`

**权限标识：** `system:tenantPackage:edit`  
**角色要求：** `superadmin`

**请求参数：**

```json
{
  "id": 1,
  "status": "1"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| id | Long | 是 | 套餐主键 ID |
| status | String | 是 | 状态（0-正常 / 1-停用） |

**响应参数：**

```json
{
  "code": 200,
  "msg": "修改成功"
}
```

---

### 2.6 删除套餐

**接口地址：** `DELETE /system/tenant/package/{packageIds}`

**权限标识：** `system:tenantPackage:remove`  
**角色要求：** `superadmin`

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| packageIds | Long[] | 是 | 套餐主键 ID 数组 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "删除成功"
}
```

---

### 2.7 查询套餐下拉列表

**接口地址：** `GET /system/tenant/package/selectList`

**权限标识：** `system:tenantPackage:list`  
**角色要求：** `superadmin`

**响应参数：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": [
    {"id": 1, "name": "基础版"},
    {"id": 2, "name": "高级版"},
    {"id": 3, "name": "企业版"}
  ]
}
```

---

### 2.8 导出套餐

**接口地址：** `POST /system/tenant/package/export`

**权限标识：** `system:tenantPackage:export`  
**角色要求：** `superadmin`

**请求参数：** 同查询列表

**响应：** Excel 文件

---

## 三、错误码说明

| 错误码 | 说明 |
|-------|------|
| 400 | 请求参数错误 |
| 401 | 未授权 |
| 403 | 权限不足 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

### 业务错误码

| 错误信息 | 说明 |
|---------|------|
| 企业名称已存在 | 新增租户时企业名称重复 |
| 套餐名称已存在 | 新增套餐时套餐名称重复 |
| 不允许操作管理租户 | 尝试修改/删除默认租户（000000） |
| 租户套餐已被使用 | 尝试删除已被使用的套餐 |
| 当前租户下用户名额不足 | 租户用户数超过限制 |
| 租户已过期 | 租户超过过期时间 |

---

## 四、接口安全

### 4.1 认证要求

所有接口需要 Sa-Token 认证，通过 `X-Access-Token` 请求头传递令牌。

### 4.2 权限检查

- 租户管理接口需要 `superadmin` 角色
- 套餐管理接口需要 `superadmin` 角色
- 使用 `@SaCheckRole` 和 `@SaCheckPermission` 注解检查

### 4.3 数据加密

- 管理员密码使用 `@ApiEncrypt` 加密传输
- 敏感数据在 VO 层使用 `@Sensitive` 脱敏

### 4.4 防重复提交

- 新增、修改接口使用 `@RepeatSubmit` 防止重复提交
- 使用 `@Lock4j` 分布式锁防止并发问题

