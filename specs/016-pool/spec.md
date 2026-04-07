# 016-pool 连接池监控模块规范

## 模块信息

| 属性 | 值 |
|------|-----|
| 模块编号 | 016 |
| 模块名称 | pool |
| 中文名称 | 连接池监控 |
| 所属系统 | RuoYi-Vue-Plus |
| 模块类型 | 监控管理 |
| 技术栈 | HikariCP + MyBatis Plus |

## 功能概述

连接池监控模块用于监控数据库连接池的运行状态，提供连接数、活跃连接、等待连接等关键指标，帮助运维人员优化数据库连接配置。

## 功能清单

### 1. 连接池状态监控
- 连接池名称
- 连接池类型
- 连接池状态

### 2. 连接数监控
- 总连接数
- 活跃连接数
- 空闲连接数
- 等待连接数

### 3. 连接池配置
- 最大连接数
- 最小空闲连接数
- 连接超时时间
- 空闲超时时间

### 4. 连接性能统计
- 连接创建次数
- 连接关闭次数
- 平均连接等待时间
- 连接使用率

### 5. SQL 执行统计
- SQL 执行次数
- SQL 执行时间
- 慢 SQL 统计

## 接口规范

### 获取连接池信息
```
GET /monitor/pool
```
**响应数据**:
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
      "min": 10
    }]
  }
}
```

### 获取 SQL 统计
```
GET /monitor/pool/sql-stats
```

## 数据模型

### PoolInfo
```java
public class PoolInfo {
    private String name;         // 连接池名称
    private Integer active;      // 活跃连接数
    private Integer idle;        // 空闲连接数
    private Integer waiting;     // 等待连接数
    private Integer max;         // 最大连接数
    private Integer min;         // 最小连接数
    private Long createCount;    // 创建次数
    private Long destroyCount;   // 销毁次数
}
```

### SqlStats
```java
public class SqlStats {
    private String sql;          // SQL 语句
    private Long executeCount;   // 执行次数
    private Long totalTime;      // 总耗时
    private Long avgTime;        // 平均耗时
}
```

## 权限配置

| 权限标识 | 权限名称 | 说明 |
|----------|----------|------|
| monitor:pool:list | 连接池监控 | 查看连接池信息 |

## 技术实现要点

### 1. 连接池监控
- 使用 HikariCP MBean 获取连接池指标
- 使用 JMX 监控连接池状态
- 定时采集连接池数据

### 2. SQL 监控
- 使用 MyBatis 拦截器记录 SQL
- 统计 SQL 执行时间和次数
- 识别慢 SQL

### 3. 告警机制
- 连接数超过阈值告警
- 连接等待超时告警
- 慢 SQL 告警

## 依赖模块

- ruoyi-common-mybatis: MyBatis 扩展
- ruoyi-common-core: 核心工具类

## 外部依赖

- **HikariCP**: 数据库连接池
- **MyBatis Plus**: ORM 框架
- **JMX**: Java 管理扩展

## 监控指标

- 连接池使用率
- 活跃连接数
- 等待连接数
- SQL 平均执行时间
- 慢 SQL 数量

## 配置建议

| 配置项 | 建议值 | 说明 |
|--------|--------|------|
| maximum-pool-size | 50 | 最大连接数 |
| minimum-idle | 10 | 最小空闲连接 |
| connection-timeout | 30000 | 连接超时 (ms) |
| idle-timeout | 600000 | 空闲超时 (ms) |
| max-lifetime | 1800000 | 最大生命周期 (ms) |
