# 016-pool 数据模型文档

## 数据模型

### PoolInfo (连接池信息)

| 字段 | 类型 | 说明 |
|------|------|------|
| name | String | 连接池名称 |
| active | Integer | 活跃连接数 |
| idle | Integer | 空闲连接数 |
| waiting | Integer | 等待连接数 |
| max | Integer | 最大连接数 |
| min | Integer | 最小连接数 |
| createCount | Long | 创建次数 |
| destroyCount | Long | 销毁次数 |
| usage | Double | 使用率 (%) |

### SqlStats (SQL 统计)

| 字段 | 类型 | 说明 |
|------|------|------|
| sql | String | SQL 语句 |
| executeCount | Long | 执行次数 |
| totalTime | Long | 总耗时 (ms) |
| avgTime | Long | 平均耗时 (ms) |
| maxTime | Long | 最大耗时 (ms) |

## 数据来源

| 数据 | 来源 |
|------|------|
| 连接池指标 | HikariCP MBean |
| SQL 统计 | MyBatis 拦截器 |
| 慢 SQL | SQL 执行时间过滤 |

## 数据更新频率

| 数据类型 | 更新频率 |
|----------|----------|
| 连接池状态 | 5 秒 |
| SQL 统计 | 实时累加 |
| 慢 SQL | 实时记录 |
