# 017-代码生成 - API 接口

**模块编号：** 017  
**模块名称：** 代码生成  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、接口概述

**基础路径：** `/tool/gen`  
**认证方式：** Sa-Token  
**数据格式：** JSON

---

## 二、接口清单

| 接口名称 | 请求方式 | 接口路径 | 权限标识 | 说明 |
|---------|---------|---------|---------|------|
| 代码生成列表 | GET | `/tool/gen/list` | `tool:gen:list` | 分页查询代码生成列表 |
| 代码生成详情 | GET | `/tool/gen/{tableId}` | `tool:gen:query` | 获取代码生成配置详情 |
| 数据库表列表 | GET | `/tool/gen/db/list` | `tool:gen:list` | 查询数据库表列表 |
| 表字段列表 | GET | `/tool/gen/column/{tableId}` | `tool:gen:list` | 查询表字段配置列表 |
| 导入表结构 | POST | `/tool/gen/importTable` | `tool:gen:import` | 从数据库导入表 |
| 修改配置 | PUT | `/tool/gen` | `tool:gen:edit` | 修改代码生成配置 |
| 删除配置 | DELETE | `/tool/gen/{tableIds}` | `tool:gen:remove` | 删除代码生成配置 |
| 预览代码 | GET | `/tool/gen/preview/{tableId}` | `tool:gen:preview` | 预览生成代码 |
| 下载代码 | GET | `/tool/gen/download/{tableId}` | `tool:gen:code` | 下载代码 ZIP |
| 生成代码 | GET | `/tool/gen/genCode/{tableId}` | `tool:gen:code` | 生成到自定义路径 |
| 同步数据库 | GET | `/tool/gen/synchDb/{tableId}` | `tool:gen:edit` | 同步数据库表结构 |
| 批量生成 | GET | `/tool/gen/batchGenCode` | `tool:gen:code` | 批量下载代码 |
| 数据源列表 | GET | `/tool/gen/getDataNames` | `tool:gen:list` | 获取数据源名称列表 |

---

## 三、接口详细定义

### 3.1 代码生成列表

**接口：** `GET /tool/gen/list`

**权限：** `tool:gen:list`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| dataName | String | 否 | 数据源名称 |
| tableName | String | 否 | 表名称（模糊匹配） |
| tableComment | String | 否 | 表描述（模糊匹配） |
| beginTime | String | 否 | 开始时间（yyyy-MM-dd） |
| endTime | String | 否 | 结束时间（yyyy-MM-dd） |
| pageNum | Integer | 否 | 页码（默认 1） |
| pageSize | Integer | 否 | 每页数量（默认 10） |

**响应示例：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "rows": [
      {
        "tableId": 1,
        "dataName": "master",
        "tableName": "sys_user",
        "tableComment": "用户信息表",
        "className": "SysUser",
        "tplCategory": "crud",
        "packageName": "org.dromara.system",
        "moduleName": "system",
        "businessName": "user",
        "functionName": "用户",
        "functionAuthor": "Lion Li",
        "genType": "0",
        "genPath": "",
        "createTime": "2026-03-13 10:00:00",
        "updateTime": "2026-03-13 12:00:00"
      }
    ],
    "total": 1,
    "pageNum": 1,
    "pageSize": 10,
    "pages": 1
  }
}
```

---

### 3.2 代码生成详情

**接口：** `GET /tool/gen/{tableId}`

**权限：** `tool:gen:query`

**路径参数：**
- `tableId`: Long - 表 ID

**响应示例：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "info": {
      "tableId": 1,
      "tableName": "sys_user",
      "tableComment": "用户信息表",
      "className": "SysUser",
      "columns": [...]
    },
    "rows": [
      {
        "columnId": 1,
        "columnName": "user_id",
        "columnComment": "用户 ID",
        "javaType": "Long",
        "javaField": "userId",
        "isPk": "1",
        "isIncrement": "1"
      }
    ],
    "tables": [...]
  }
}
```

---

### 3.3 数据库表列表

**接口：** `GET /tool/gen/db/list`

**权限：** `tool:gen:list`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| dataName | String | 否 | 数据源名称 |
| tableName | String | 否 | 表名称（模糊匹配） |
| tableComment | String | 否 | 表描述（模糊匹配） |
| pageNum | Integer | 否 | 页码 |
| pageSize | Integer | 否 | 每页数量 |

**响应示例：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "rows": [
      {
        "tableName": "test_order",
        "tableComment": "订单表",
        "engine": "InnoDB",
        "charset": "utf8mb4",
        "createTime": "2026-03-13 10:00:00"
      }
    ],
    "total": 10,
    "pageNum": 1,
    "pageSize": 10
  }
}
```

---

### 3.4 表字段列表

**接口：** `GET /tool/gen/column/{tableId}`

**权限：** `tool:gen:list`

**路径参数：**
- `tableId`: Long - 表 ID

**响应示例：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "rows": [
      {
        "columnId": 1,
        "columnName": "user_id",
        "columnComment": "用户 ID",
        "columnType": "bigint",
        "javaType": "Long",
        "javaField": "userId",
        "isPk": "1",
        "isIncrement": "1",
        "isRequired": "0",
        "isInsert": "0",
        "isEdit": "0",
        "isList": "0",
        "isQuery": "0",
        "queryType": "EQ",
        "htmlType": "input",
        "dictType": "",
        "sort": 1
      }
    ],
    "total": 10
  }
}
```

---

### 3.5 导入表结构

**接口：** `POST /tool/gen/importTable`

**权限：** `tool:gen:import`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| tables | String | 是 | 表名串（逗号分隔） |
| dataName | String | 是 | 数据源名称 |

**请求示例：**

```json
{
  "tables": "test_order,test_order_item",
  "dataName": "master"
}
```

**响应示例：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

---

### 3.6 修改代码生成配置

**接口：** `PUT /tool/gen`

**权限：** `tool:gen:edit`

**请求体：**

```json
{
  "tableId": 1,
  "dataName": "master",
  "tableName": "sys_user",
  "tableComment": "用户信息表",
  "className": "SysUser",
  "tplCategory": "crud",
  "packageName": "org.dromara.system",
  "moduleName": "system",
  "businessName": "user",
  "functionName": "用户",
  "functionAuthor": "Lion Li",
  "genType": "0",
  "genPath": "",
  "remark": "备注",
  "columns": [
    {
      "columnId": 1,
      "columnComment": "用户 ID",
      "javaType": "Long",
      "javaField": "userId",
      "isPk": "1",
      "isIncrement": "1",
      "isRequired": "1",
      "isInsert": "0",
      "isEdit": "0",
      "isList": "1",
      "isQuery": "1",
      "queryType": "EQ",
      "htmlType": "input",
      "dictType": "",
      "sort": 1
    }
  ]
}
```

**响应示例：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

---

### 3.7 删除代码生成配置

**接口：** `DELETE /tool/gen/{tableIds}`

**权限：** `tool:gen:remove`

**路径参数：**
- `tableIds`: Long[] - 表 ID 数组（逗号分隔）

**响应示例：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

---

### 3.8 预览代码

**接口：** `GET /tool/gen/preview/{tableId}`

**权限：** `tool:gen:preview`

**路径参数：**
- `tableId`: Long - 表 ID

**响应示例：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "SysUser.java": "package org.dromara.system.domain;...",
    "SysUserMapper.java": "package org.dromara.system.mapper;...",
    "SysUserServiceImpl.java": "package org.dromara.system.service.impl;...",
    "SysUserController.java": "package org.dromara.system.controller;...",
    "SysUserMapper.xml": "<?xml version=\"1.0\" encoding=\"UTF-8\"?>...",
    "index.vue": "<template>...",
    "index.ts": "import request from '@/utils/request'...",
    "schema.sql": "DROP TABLE IF EXISTS sys_user;..."
  }
}
```

---

### 3.9 下载代码

**接口：** `GET /tool/gen/download/{tableId}`

**权限：** `tool:gen:code`

**路径参数：**
- `tableId`: Long - 表 ID

**响应：** ZIP 文件（application/octet-stream）

**文件名：** `ruoyi.zip`

---

### 3.10 生成代码（自定义路径）

**接口：** `GET /tool/gen/genCode/{tableId}`

**权限：** `tool:gen:code`

**路径参数：**
- `tableId`: Long - 表 ID

**响应示例：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

---

### 3.11 同步数据库

**接口：** `GET /tool/gen/synchDb/{tableId}`

**权限：** `tool:gen:edit`

**路径参数：**
- `tableId`: Long - 表 ID

**响应示例：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

---

### 3.12 批量生成代码

**接口：** `GET /tool/gen/batchGenCode`

**权限：** `tool:gen:code`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| tableIdStr | String | 是 | 表 ID 串（逗号分隔） |

**响应：** ZIP 文件（application/octet-stream）

---

### 3.13 获取数据源名称列表

**接口：** `GET /tool/gen/getDataNames`

**权限：** `tool:gen:list`

**响应示例：**

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": ["master", "slave1", "slave2"]
}
```

---

## 四、错误码说明

| 错误码 | 说明 |
|-------|------|
| 200 | 成功 |
| 401 | 未授权 |
| 403 | 无权限 |
| 500 | 服务器错误 |

---

## 五、安全要求

1. **权限验证**：所有接口需要 Sa-Token 权限验证
2. **防重复提交**：关键操作使用 `@RepeatSubmit` 注解
3. **分布式锁**：导入/同步操作使用 `@Lock4j` 防止并发
4. **日志记录**：关键操作记录操作日志

---

**文档版本：** 1.0  
**创建日期：** 2026-03-13  
**审核状态：** 待审核
