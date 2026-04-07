# 004-部门管理 - API 清单

**模块编号：** 004  
**模块名称：** 部门管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、部门管理接口

### 1.1 查询部门列表

**接口地址：** `GET /system/dept/list`

**权限标识：** `system:dept:list`

**响应参数：** 树形结构

```json
{
  "code": 200,
  "data": [
    {
      "deptId": 100,
      "deptName": "总公司",
      "children": [
        {
          "deptId": 101,
          "deptName": "研发部"
        }
      ]
    }
  ]
}
```

---

### 1.2 获取部门详情

**接口地址：** `GET /system/dept/{deptId}`

**权限标识：** `system:dept:query`

---

### 1.3 新增部门

**接口地址：** `POST /system/dept`

**权限标识：** `system:dept:add`

**请求参数：**

```json
{
  "parentId": 100,
  "deptName": "前端组",
  "orderNum": 1,
  "leader": 1,
  "phone": "13800138000",
  "email": "front@example.com",
  "status": "0"
}
```

---

### 1.4 修改部门

**接口地址：** `PUT /system/dept`

**权限标识：** `system:dept:edit`

---

### 1.5 删除部门

**接口地址：** `DELETE /system/dept/{deptIds}`

**权限标识：** `system:dept:remove`

---

### 1.6 修改部门状态

**接口地址：** `PUT /system/dept/changeStatus`

**权限标识：** `system:dept:edit`

---

## 二、错误码说明

| 错误信息 | 说明 |
|---------|------|
| 部门名称已存在 | 同一父部门下名称重复 |
| 存在子部门，不允许删除 | 删除前检查 |
| 部门下存在用户，不允许删除 | 删除前检查 |
| 不允许修改为自身的子部门 | 循环引用检查 |

