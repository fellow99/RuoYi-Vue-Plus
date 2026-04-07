# 015-server API 接口文档

## 接口列表

| 接口 | 方法 | 权限 | 说明 |
|------|------|------|------|
| /monitor/server | GET | monitor:server:list | 获取服务器信息 |

## 接口详情

### 获取服务器信息

**接口**: `GET /monitor/server`

**权限**: `monitor:server:list`

**响应**:
```json
{
  "code": 200,
  "data": {
    "cpu": {
      "cpuNum": 8,
      "used": 25.5,
      "sys": 10.2,
      "wait": 1.3,
      "free": 63.0
    },
    "memory": {
      "total": 16.0,
      "used": 8.5,
      "free": 7.5,
      "usage": 53.1
    },
    "disk": [{
      "dirName": "/",
      "total": 500.0,
      "used": 200.0,
      "free": 300.0,
      "usage": 40.0
    }],
    "jvm": {
      "version": "17.0.0",
      "total": 512,
      "max": 1024,
      "used": 256
    }
  }
}
```
