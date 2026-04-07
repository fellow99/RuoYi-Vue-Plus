# 014-cache API 接口文档

## 接口列表

| 接口 | 方法 | 权限 | 说明 |
|------|------|------|------|
| /monitor/cache | GET | monitor:cache:list | 获取缓存信息 |
| /monitor/cache/list | GET | monitor:cache:list | 获取缓存列表 |
| /monitor/cache/info/{cacheName} | GET | monitor:cache:query | 获取缓存详情 |
| /monitor/cache/{cacheName} | DELETE | monitor:cache:clear | 清理缓存 |

## 接口详情

### 1. 获取缓存信息

**接口**: `GET /monitor/cache`

**权限**: `monitor:cache:list`

**响应**:
```json
{
  "code": 200,
  "data": {
    "info": {
      "redis_version": "7.0.0",
      "used_memory_human": "100M",
      "connected_clients": "50"
    },
    "dbSize": 10000,
    "commandStats": [
      {"name": "get", "value": "100000"},
      {"name": "set", "value": "50000"}
    ]
  }
}
```

### 2. 获取缓存列表

**接口**: `GET /monitor/cache/list`

**响应**: 缓存名称列表

### 3. 获取缓存详情

**接口**: `GET /monitor/cache/info/{cacheName}`

**响应**: 缓存详细信息

### 4. 清理缓存

**接口**: `DELETE /monitor/cache/{cacheName}`

**响应**: 操作结果
