# 003-角色管理 - API 清单

**模块编号：** 003  
**模块名称：** 角色管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、角色管理接口

### 1.1 查询角色列表

**接口地址：** `GET /system/role/list`

**权限标识：** `system:role:list`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| roleName | String | 否 | 角色名称（模糊） |
| roleKey | String | 否 | 角色标识（模糊） |
| status | String | 否 | 状态 |
| pageNum | Integer | 否 | 页码 |
| pageSize | Integer | 否 | 每页数量 |

---

### 1.2 获取角色详情

**接口地址：** `GET /system/role/{roleId}`

**权限标识：** `system:role:query`

---

### 1.3 新增角色

**接口地址：** `POST /system/role`

**权限标识：** `system:role:add`

**请求参数：**

```json
{
  "roleName": "普通用户",
  "roleKey": "common",
  "roleSort": 2,
  "dataScope": "3",
  "menuIds": [1, 2, 3],
  "status": "0"
}
```

---

### 1.4 修改角色

**接口地址：** `PUT /system/role`

**权限标识：** `system:role:edit`

---

### 1.5 删除角色

**接口地址：** `DELETE /system/role/{roleIds}`

**权限标识：** `system:role:remove`

---

### 1.6 修改角色状态

**接口地址：** `PUT /system/role/changeStatus`

**权限标识：** `system:role:edit`

---

### 1.7 分配菜单权限

**接口地址：** `PUT /system/role/authMenu`

**权限标识：** `system:role:edit`

**请求参数：**

```json
{
  "roleId": 2,
  "menuIds": [1, 2, 3, 4, 5]
}
```

---

### 1.8 分配数据权限

**接口地址：** `PUT /system/role/dataScope`

**权限标识：** `system:role:edit`

**请求参数：**

```json
{
  "roleId": 2,
  "dataScope": "2",
  "deptIds": [100, 101]
}
```

---

### 1.9 查询角色下的用户

**接口地址：** `GET /system/role/authUser/allocatedList`

**权限标识：** `system:role:query`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| roleId | Long | 是 | 角色 ID |
| userName | String | 否 | 用户账号 |
| phonenumber | String | 否 | 手机号 |

---

### 1.10 添加用户到角色

**接口地址：** `PUT /system/role/authUser/selectAll`

**权限标识：** `system:role:edit`

**请求参数：**

```json
{
  "roleId": 2,
  "userIds": "1,2,3"
}
```

---

### 1.11 从角色移除用户

**接口地址：** `PUT /system/role/authUser/cancel`

**权限标识：** `system:role:edit`

---

## 二、错误码说明

| 错误信息 | 说明 |
|---------|------|
| 角色名称已存在 | 角色名称重复 |
| 权限字符已存在 | roleKey 重复 |
| 不允许修改超级管理员角色 | 尝试修改 admin 角色 |
| 角色下已有用户，不能删除 | 删除前检查 |

