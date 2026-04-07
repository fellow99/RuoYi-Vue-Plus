# 014-cache 缓存管理模块规范

## 模块信息

| 属性 | 值 |
|------|-----|
| 模块编号 | 014 |
| 模块名称 | cache |
| 中文名称 | 缓存管理 |
| 所属系统 | RuoYi-Vue-Plus |
| 模块类型 | 监控管理 |
| 技术栈 | Redis + Spring Data Redis |

## 功能概述

缓存管理模块用于实时监控 Redis 缓存的运行状态，提供缓存信息查询、命令统计和缓存管理功能，帮助运维人员了解缓存使用情况，优化系统性能。

## 功能清单

### 1. 缓存信息查询
- **Redis 信息**: 查看 Redis 服务器基本信息
- **内存使用**: 查看 Redis 内存占用情况
- **键数量统计**: 查看数据库键值对数量
- **连接信息**: 查看客户端连接情况

### 2. 命令统计
- **命令调用次数**: 统计各 Redis 命令的调用次数
- **命令执行时间**: 统计命令执行耗时
- **性能分析**: 分析 Redis 性能瓶颈

### 3. 缓存监控
- **命中率监控**: 监控缓存命中情况
- **内存监控**: 监控内存使用趋势
- **连接数监控**: 监控客户端连接数

### 4. 缓存管理
- **缓存清理**: 支持清理指定缓存
- **键查询**: 支持按 pattern 查询键
- **键详情**: 查看键的详细信息

## 接口规范

### 获取缓存信息
```
GET /monitor/cache
```
**响应数据**:
```json
{
  "code": 200,
  "data": {
    "info": {...},
    "dbSize": 1000,
    "commandStats": [
      {"name": "get", "value": "10000"},
      {"name": "set", "value": "5000"}
    ]
  }
}
```

### 获取缓存列表
```
GET /monitor/cache/list
```

### 获取缓存详情
```
GET /monitor/cache/info/{cacheName}
```

### 清理缓存
```
DELETE /monitor/cache/{cacheName}
```

## 数据模型

### CacheInfo
```java
public class CacheInfo {
    /** Redis 信息 */
    private Properties info;
    /** 键数量 */
    private Long dbSize;
    /** 命令统计 */
    private List<Map<String, String>> commandStats;
}
```

## 权限配置

| 权限标识 | 权限名称 | 说明 |
|----------|----------|------|
| monitor:cache:list | 缓存列表 | 查看缓存信息 |
| monitor:cache:query | 缓存查询 | 查询缓存详情 |
| monitor:cache:clear | 缓存清理 | 清理缓存 |

## 技术实现要点

### 1. Redis 连接
- 使用 RedissonConnectionFactory 获取连接
- 连接使用后及时释放

### 2. 信息查询
- 使用 INFO 命令获取 Redis 信息
- 使用 DBSIZE 命令获取键数量
- 使用 INFO commandstats 获取命令统计

### 3. 性能优化
- 缓存监控数据
- 避免频繁查询 Redis
- 使用连接池管理连接

## 依赖模块

- ruoyi-common-redis: Redis 工具类
- ruoyi-common-core: 核心工具类

## 外部依赖

- **Redis**: 缓存服务
- **Spring Data Redis**: Redis 客户端

## 监控指标

- Redis 内存使用率
- 缓存命中率
- 命令执行 QPS
- 客户端连接数
