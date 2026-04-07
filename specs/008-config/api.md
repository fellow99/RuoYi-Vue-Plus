# 008-参数配置 - API 接口

**模块编号：** 008  
**最后更新：** 2026-03-13

---

## 一、接口列表

### 1.1 获取参数列表

**接口：** `GET /system/config/list`

**权限：** `system:config:list`

**参数：** configName, configKey, configType, pageNum, pageSize

### 1.2 获取参数详情

**接口：** `GET /system/config/{configId}`

**权限：** `system:config:query`

### 1.3 根据键名查询参数值

**接口：** `GET /system/config/configKey/{configKey}`

**权限：** 无

**响应：** `{ "code": 200, "msg": "操作成功", "data": "参数值" }`

### 1.4 新增参数

**接口：** `POST /system/config`

**权限：** `system:config:add`

**参数：** configName, configKey, configValue, configType, remark

### 1.5 修改参数

**接口：** `PUT /system/config`

**权限：** `system:config:edit`

### 1.6 删除参数

**接口：** `DELETE /system/config/{configIds}`

**权限：** `system:config:remove`

### 1.7 刷新参数缓存

**接口：** `DELETE /system/config/refreshCache`

**权限：** `system:config:remove`

---

## 二、错误码

| 错误码 | 说明 |
|--------|------|
| 400 | 参数键名已存在 / 系统内置参数不允许删除 |
| 200 | 操作成功 |

