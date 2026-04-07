# 014-cache 数据模型文档

## 数据结构

### CacheInfo (缓存信息)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| info | Properties | Redis 服务器信息 |
| dbSize | Long | 键数量 |
| commandStats | List<Map> | 命令统计 |

### Redis Info 字段

| 字段 | 说明 |
|------|------|
| redis_version | Redis 版本 |
| used_memory | 已用内存 |
| used_memory_human | 人类可读内存 |
| connected_clients | 连接数 |
| ops_per_second | 每秒操作数 |

## 缓存分类

| 缓存类型 | 说明 | 示例 |
|----------|------|------|
| 用户缓存 | 用户信息缓存 | user:123 |
| 权限缓存 | 权限数据缓存 | permission:123 |
| 配置缓存 | 系统配置缓存 | config:key |
| 字典缓存 | 字典数据缓存 | dict:type |
| 在线用户 | 在线会话缓存 | online_token:xxx |
