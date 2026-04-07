# 005-岗位管理 - API 清单

**模块编号：** 005  
**模块名称：** 岗位管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、岗位管理接口

### 1.1 查询岗位列表

**接口地址：** `GET /system/post/list`

**权限标识：** `system:post:list`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| postCode | String | 否 | 岗位编码（模糊） |
| postName | String | 否 | 岗位名称（模糊） |
| status | String | 否 | 状态 |
| pageNum | Integer | 否 | 页码 |
| pageSize | Integer | 否 | 每页数量 |

---

### 1.2 获取岗位详情

**接口地址：** `GET /system/post/{postId}`

**权限标识：** `system:post:query`

---

### 1.3 新增岗位

**接口地址：** `POST /system/post`

**权限标识：** `system:post:add`

**请求参数：**

```json
{
  "postCode": "dev",
  "postName": "开发工程师",
  "postSort": 1,
  "status": "0",
  "remark": "技术岗位"
}
```

---

### 1.4 修改岗位

**接口地址：** `PUT /system/post`

**权限标识：** `system:post:edit`

---

### 1.5 删除岗位

**接口地址：** `DELETE /system/post/{postIds}`

**权限标识：** `system:post:remove`

---

### 1.6 修改岗位状态

**接口地址：** `PUT /system/post/changeStatus`

**权限标识：** `system:post:edit`

---

## 二、错误码说明

| 错误信息 | 说明 |
|---------|------|
| 岗位编码已存在 | postCode 重复 |
| 岗位名称已存在 | postName 重复 |
| 岗位已被用户使用，不能删除 | 删除前检查 |

