# 016-pool API 接口文档

## 接口列表

| 接口 | 方法 | 权限 | 说明 |
|------|------|------|------|
| /monitor/pool | GET | monitor:pool:list | 获取连接池信息 |
| /monitor/pool/sql-stats | GET | monitor:pool:list | 获取 SQL 统计 |
| /monitor/pool/slow-sql | GET | monitor:pool:list | 获取慢 SQL |

## 接口详情

### 1. 获取连接池信息

**接口**: `GET /monitor/pool`

**权限**: `monitor:pool:list`

**响应**:
```json
{
  "code": 200,
  "data": {
    "pools": [{
      "name": "HikariPool-1",
      "active": 10,
      "idle": 5,
      "waiting": 0,
      "max": 50,
      "min": 10,
      "usage": 20.0
    }]
  }
}
```

### 2. 获取 SQL 统计

**接口**: `GET /monitor/pool/sql-stats`

**响应**:
```json
{
  "code": 200,
  "data": [{
    "sql": "SELECT * FROM user WHERE id = ?",
    "executeCount": 1000,
    "totalTime": 5000,
    "avgTime": 5
  }]
}
```

### 3. 获取慢 SQL

**接口**: `GET /monitor/pool/slow-sql`

**请求参数**:
| 参数 | 类型 | 说明 |
|------|------|------|
| threshold | Long | 慢 SQL 阈值 (ms) |

**响应**: 慢 SQL 列表
