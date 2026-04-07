# 012-online 数据模型文档

## 实体列表

| 实体名称 | 说明 | 存储位置 |
|----------|------|----------|
| SysUserOnline | 在线用户会话 | Redis |
| UserOnlineDTO | 在线用户数据传输对象 | Redis |

## SysUserOnline

### 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| tokenId | String | 是 | 会话编号，Sa-Token 生成的唯一标识 |
| deptName | String | 否 | 部门名称 |
| userName | String | 是 | 用户名称 |
| clientKey | String | 是 | 客户端标识，用于多端登录 |
| deviceType | String | 是 | 设备类型（pc/mobile/app） |
| ipaddr | String | 是 | 登录 IP 地址 |
| loginLocation | String | 否 | 登录地理位置 |
| browser | String | 否 | 浏览器类型 |
| os | String | 否 | 操作系统 |
| loginTime | Long | 是 | 登录时间戳 |

### Redis 存储结构

```
Key: online_token:{tokenId}
Type: Hash
TTL: 根据 Sa-Token 配置（默认 30 天，自动续期）
```

### 索引设计

| 索引字段 | 索引类型 | 说明 |
|----------|----------|------|
| online_token:* | Key Pattern | 用于查询所有在线用户 |

## UserOnlineDTO

### 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| tokenId | String | 是 | 会话编号 |
| userId | Long | 是 | 用户 ID |
| userName | String | 是 | 用户名称 |
| deptName | String | 否 | 部门名称 |
| clientKey | String | 是 | 客户端标识 |
| deviceType | String | 是 | 设备类型 |
| ipaddr | String | 是 | 登录 IP |
| loginLocation | String | 否 | 登录地点 |
| browser | String | 否 | 浏览器 |
| os | String | 否 | 操作系统 |
| loginTime | Long | 是 | 登录时间 |

## 数据关系

```
UserOnlineDTO (Redis) 
    ↓ 转换
SysUserOnline (返回给前端)
```

## 数据生命周期

1. **创建**: 用户登录时创建 UserOnlineDTO 并存入 Redis
2. **读取**: 查询在线用户列表时从 Redis 读取
3. **更新**: Token 续期时自动更新 TTL
4. **删除**: 用户退出或强制踢出时删除

## 数据量估算

| 指标 | 估算值 | 说明 |
|------|--------|------|
| 单个对象大小 | ~500 bytes | 序列化后 |
| 并发在线用户 | 10,000 | 根据系统规模 |
| Redis 内存占用 | ~5MB | 10,000 用户 |
