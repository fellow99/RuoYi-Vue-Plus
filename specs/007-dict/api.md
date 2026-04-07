# 007-字典管理 - API 接口

**模块编号：** 007  
**模块名称：** 字典管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、接口概述

- **基础路径：** `/system/dict`
- **认证方式：** Sa-Token 会话认证
- **数据格式：** application/json

---

## 二、字典类型接口

### 2.1 获取字典类型列表

**接口地址：** `GET /system/dict/type/list`

**认证要求：** `system:dict:list`

**请求参数：**

| 参数名 | 类型 | 位置 | 必填 | 说明 |
|--------|------|------|------|------|
| dictName | String | query | 否 | 字典名称（模糊） |
| dictType | String | query | 否 | 字典类型（模糊） |
| pageNum | Integer | query | 否 | 页码 |
| pageSize | Integer | query | 否 | 每页大小 |

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "rows": [
    {
      "dictId": 1,
      "dictName": "用户性别",
      "dictType": "sys_user_sex",
      "remark": ""
    }
  ],
  "total": 1
}
```

---

### 2.2 获取字典类型详情

**接口地址：** `GET /system/dict/type/{dictId}`

**认证要求：** `system:dict:query`

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "dictId": 1,
    "dictName": "用户性别",
    "dictType": "sys_user_sex",
    "remark": ""
  }
}
```

---

### 2.3 新增字典类型

**接口地址：** `POST /system/dict/type`

**认证要求：** `system:dict:add`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dictName | String | 是 | 字典名称（2-100 字符） |
| dictType | String | 是 | 字典类型（2-100 字符，唯一） |
| remark | String | 否 | 备注 |

**请求示例：**

```json
{
  "dictName": "用户性别",
  "dictType": "sys_user_sex",
  "remark": ""
}
```

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

---

### 2.4 修改字典类型

**接口地址：** `PUT /system/dict/type`

**认证要求：** `system:dict:edit`

**请求参数：** 同新增，需包含 dictId

---

### 2.5 删除字典类型

**接口地址：** `DELETE /system/dict/type/{dictIds}`

**认证要求：** `system:dict:remove`

**路径参数：** dictIds - 字典 ID 列表（逗号分隔）

---

### 2.6 刷新字典缓存

**接口地址：** `DELETE /system/dict/type/refreshCache`

**认证要求：** `system:dict:remove`

---

### 2.7 获取字典类型下拉列表

**接口地址：** `GET /system/dict/type/optionselect`

**认证要求：** 无

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "dictId": 1,
      "dictName": "用户性别",
      "dictType": "sys_user_sex"
    }
  ]
}
```

---

## 三、字典数据接口

### 3.1 获取字典数据列表

**接口地址：** `GET /system/dict/data/list`

**认证要求：** `system:dict:list`

**请求参数：**

| 参数名 | 类型 | 位置 | 必填 | 说明 |
|--------|------|------|------|------|
| dictType | String | query | 否 | 字典类型 |
| dictLabel | String | query | 否 | 字典标签（模糊） |
| pageNum | Integer | query | 否 | 页码 |
| pageSize | Integer | query | 否 | 每页大小 |

---

### 3.2 获取字典数据详情

**接口地址：** `GET /system/dict/data/{dictCode}`

**认证要求：** `system:dict:query`

---

### 3.3 根据类型查询字典数据

**接口地址：** `GET /system/dict/data/type/{dictType}`

**认证要求：** 无（公开接口）

**响应参数：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "dictCode": 1,
      "dictLabel": "男",
      "dictValue": "1",
      "dictSort": 1,
      "isDefault": "Y"
    },
    {
      "dictCode": 2,
      "dictLabel": "女",
      "dictValue": "2",
      "dictSort": 2,
      "isDefault": "N"
    }
  ]
}
```

---

### 3.4 新增字典数据

**接口地址：** `POST /system/dict/data`

**认证要求：** `system:dict:add`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dictType | String | 是 | 字典类型 |
| dictLabel | String | 是 | 字典标签 |
| dictValue | String | 是 | 字典键值 |
| dictSort | Integer | 是 | 字典排序 |
| cssClass | String | 否 | 样式属性 |
| listClass | String | 否 | 表格样式 |
| isDefault | String | 否 | 是否默认（Y/N） |
| status | String | 否 | 状态（0/1） |
| remark | String | 否 | 备注 |

---

### 3.5 修改字典数据

**接口地址：** `PUT /system/dict/data`

**认证要求：** `system:dict:edit`

---

### 3.6 删除字典数据

**接口地址：** `DELETE /system/dict/data/{dictCodes}`

**认证要求：** `system:dict:remove`

---

## 四、错误码

| 错误码 | 说明 |
|--------|------|
| 200 | 操作成功 |
| 400 | 字典类型已存在 / 字典键值已存在 |
| 401 | 未授权 |
| 403 | 无权限 |
| 500 | 服务器错误 |

