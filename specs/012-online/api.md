# 012-online API 接口文档

## 接口列表

| 接口 | 方法 | 权限 | 说明 |
|------|------|------|------|
| /monitor/online/list | GET | monitor:online:list | 查询在线用户列表 |
| /monitor/online/{tokenId} | DELETE | monitor:online:forceLogout | 强制踢出用户 |
| /monitor/online | GET | - | 获取当前用户在线设备 |
| /monitor/online/myself/{tokenId} | DELETE | - | 强退当前用户在线设备 |

---

## 接口详情

### 1. 查询在线用户列表

**接口地址**: `/monitor/online/list`

**请求方法**: `GET`

**权限要求**: `monitor:online:list`

**请求参数**:

| 参数名 | 类型 | 位置 | 必填 | 说明 |
|--------|------|------|------|------|
| ipaddr | String | Query | 否 | IP 地址，支持模糊查询 |
| userName | String | Query | 否 | 登录账号，支持模糊查询 |

**请求示例**:
```http
GET /monitor/online/list?ipaddr=192.168&userName=admin
```

**响应参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| msg | String | 提示信息 |
| data.total | Long | 总记录数 |
| data.rows | Array | 在线用户列表 |

**响应示例**:
```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "total": 1,
    "rows": [{
      "tokenId": "abc123",
      "userName": "admin",
      "deptName": "研发部门",
      "clientKey": "default",
      "deviceType": "pc",
      "ipaddr": "192.168.1.1",
      "loginLocation": "北京",
      "browser": "Chrome 120",
      "os": "Windows 10",
      "loginTime": 1705291800000
    }]
  }
}
```

---

### 2. 强制踢出用户

**接口地址**: `/monitor/online/{tokenId}`

**请求方法**: `DELETE`

**权限要求**: `monitor:online:forceLogout`

**路径参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| tokenId | String | 会话 ID |

**请求示例**:
```http
DELETE /monitor/online/abc123
```

**响应参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 状态码 |
| msg | String | 提示信息 |

**响应示例**:
```json
{
  "code": 200,
  "msg": "强退成功"
}
```

---

### 3. 获取当前用户在线设备

**接口地址**: `/monitor/online`

**请求方法**: `GET`

**权限要求**: 登录用户

**请求参数**: 无

**请求示例**:
```http
GET /monitor/online
```

**响应参数**: 同接口 1

---

### 4. 强退当前用户在线设备

**接口地址**: `/monitor/online/myself/{tokenId}`

**请求方法**: `DELETE`

**权限要求**: 登录用户

**路径参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| tokenId | String | 会话 ID |

**请求示例**:
```http
DELETE /monitor/online/myself/abc123
```

**响应示例**:
```json
{
  "code": 200,
  "msg": "操作成功"
}
```

---

## 错误码

| 错误码 | 说明 |
|--------|------|
| 200 | 成功 |
| 401 | 未登录 |
| 403 | 权限不足 |
| 500 | 服务器错误 |

## 限流策略

| 接口 | 限流规则 |
|------|----------|
| /monitor/online/list | 60 次/分钟 |
| /monitor/online/{tokenId} | 10 次/分钟 |
