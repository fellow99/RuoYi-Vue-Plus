# 002-用户管理 - API 清单

**模块编号：** 002  
**模块名称：** 用户管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、用户管理接口

### 1.1 查询用户列表

**接口地址：** `GET /system/user/list`

**权限标识：** `system:user:list`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| userName | String | 否 | 用户账号（模糊） |
| nickName | String | 否 | 用户昵称（模糊） |
| phonenumber | String | 否 | 手机号码（模糊） |
| status | String | 否 | 状态（0-正常 / 1-停用） |
| deptId | Long | 否 | 部门 ID |
| createTime | String[] | 否 | 时间范围 [开始，结束] |
| pageNum | Integer | 否 | 页码，默认 1 |
| pageSize | Integer | 否 | 每页数量，默认 10 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "rows": [
    {
      "userId": 1,
      "userName": "admin",
      "nickName": "管理员",
      "deptName": "研发部",
      "phonenumber": "138****8888",
      "email": "admin@example.com",
      "sex": "0",
      "status": "0",
      "createTime": "2026-01-01 00:00:00"
    }
  ],
  "total": 1
}
```

---

### 1.2 获取用户详情

**接口地址：** `GET /system/user/{userId}`

**权限标识：** `system:user:query`

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| userId | Long | 是 | 用户 ID |

**响应参数：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "userId": 1,
    "userName": "admin",
    "nickName": "管理员",
    "deptId": 100,
    "deptName": "研发部",
    "email": "admin@example.com",
    "phonenumber": "13800138000",
    "sex": "0",
    "avatar": 1,
    "status": "0",
    "roleIds": [1],
    "postIds": [1],
    "remark": "备注"
  }
}
```

---

### 1.3 新增用户

**接口地址：** `POST /system/user`

**权限标识：** `system:user:add`

**请求参数：**

```json
{
  "userName": "zhangsan",
  "nickName": "张三",
  "deptId": 100,
  "password": "admin123",
  "email": "zhangsan@example.com",
  "phonenumber": "13800138000",
  "sex": "0",
  "status": "0",
  "roleIds": [2],
  "postIds": [1],
  "remark": "备注"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| userName | String | 是 | 用户账号（2-30 字符） |
| nickName | String | 是 | 用户昵称 |
| deptId | Long | 是 | 部门 ID |
| password | String | 是 | 密码（5-20 字符，加密） |
| email | String | 否 | 邮箱 |
| phonenumber | String | 否 | 手机号 |
| sex | String | 否 | 性别（0-男/1-女/2-未知） |
| status | String | 否 | 状态（0-正常/1-停用） |
| roleIds | Long[] | 是 | 角色 ID 数组 |
| postIds | Long[] | 否 | 岗位 ID 数组 |
| remark | String | 否 | 备注 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "新增成功"
}
```

---

### 1.4 修改用户

**接口地址：** `PUT /system/user`

**权限标识：** `system:user:edit`

**请求参数：**

```json
{
  "userId": 2,
  "userName": "zhangsan",
  "nickName": "张三",
  "deptId": 100,
  "email": "zhangsan@example.com",
  "phonenumber": "13800138000",
  "sex": "0",
  "status": "0",
  "roleIds": [2],
  "postIds": [1],
  "remark": "更新备注"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| userId | Long | 是 | 用户 ID |
| userName | String | 是 | 用户账号（不可修改） |
| nickName | String | 是 | 用户昵称 |
| deptId | Long | 是 | 部门 ID |
| email | String | 否 | 邮箱 |
| phonenumber | String | 否 | 手机号 |
| sex | String | 否 | 性别 |
| status | String | 否 | 状态 |
| roleIds | Long[] | 是 | 角色 ID 数组 |
| postIds | Long[] | 否 | 岗位 ID 数组 |
| remark | String | 否 | 备注 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "修改成功"
}
```

---

### 1.5 删除用户

**接口地址：** `DELETE /system/user/{userIds}`

**权限标识：** `system:user:remove`

**路径参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| userIds | Long[] | 是 | 用户 ID 数组 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "删除成功"
}
```

---

### 1.6 重置密码

**接口地址：** `PUT /system/user/resetPwd`

**权限标识：** `system:user:resetPwd`

**请求参数：**

```json
{
  "userId": 2,
  "password": "new123456"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| userId | Long | 是 | 用户 ID |
| password | String | 是 | 新密码（加密传输） |

**响应参数：**

```json
{
  "code": 200,
  "msg": "重置成功"
}
```

---

### 1.7 修改用户状态

**接口地址：** `PUT /system/user/changeStatus`

**权限标识：** `system:user:edit`

**请求参数：**

```json
{
  "userId": 2,
  "status": "1"
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| userId | Long | 是 | 用户 ID |
| status | String | 是 | 状态（0-正常 / 1-停用） |

**响应参数：**

```json
{
  "code": 200,
  "msg": "修改成功"
}
```

---

### 1.8 授权角色

**接口地址：** `PUT /system/user/authRole`

**权限标识：** `system:user:edit`

**请求参数：**

```json
{
  "userId": 2,
  "roleIds": [2, 3]
}
```

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| userId | Long | 是 | 用户 ID |
| roleIds | Long[] | 是 | 角色 ID 数组 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "授权成功"
}
```

---

### 1.9 获取当前用户信息

**接口地址：** `GET /system/user/getInfo`

**权限标识：** 无需

**响应参数：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "user": {
      "userId": 1,
      "userName": "admin",
      "nickName": "管理员",
      "deptName": "研发部"
    },
    "roles": ["admin"],
    "permissions": ["system:user:list", "system:user:add"]
  }
}
```

---

### 1.10 获取部门树

**接口地址：** `GET /system/user/deptTree`

**权限标识：** `system:user:list`

**响应参数：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": [
    {
      "id": 100,
      "label": "研发部",
      "children": [
        {"id": 101, "label": "前端组"}
      ]
    }
  ]
}
```

---

### 1.11 导入用户

**接口地址：** `POST /system/user/importData`

**权限标识：** `system:user:import`

**请求参数：** FormData

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| file | File | 是 | Excel 文件 |
| updateSupport | Boolean | 否 | 是否更新已存在用户 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "导入成功，共 10 条，成功 10 条，失败 0 条"
}
```

---

### 1.12 导出用户

**接口地址：** `POST /system/user/export`

**权限标识：** `system:user:export`

**请求参数：** 同查询列表

**响应：** Excel 文件

---

## 二、错误码说明

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
| 用户账号已存在 | 新增用户时账号重复 |
| 手机号码已存在 | 手机号重复 |
| 邮箱已存在 | 邮箱重复 |
| 不允许修改超级管理员 | 尝试修改 userId=1 |
| 不允许删除超级管理员 | 尝试删除 userId=1 |
| 当前租户下用户名额不足 | 租户用户数超过限制 |

---

## 三、接口安全

### 3.1 认证要求

所有接口需要 Sa-Token 认证，通过 `X-Access-Token` 请求头传递令牌。

### 3.2 权限检查

使用 `@SaCheckPermission` 注解检查权限。

### 3.3 数据加密

- 密码使用 `@ApiEncrypt` 加密传输
- 敏感数据在 VO 层使用 `@Sensitive` 脱敏

### 3.4 防重复提交

- 新增、修改接口使用 `@RepeatSubmit` 防止重复提交

