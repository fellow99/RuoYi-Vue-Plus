# 013-job 数据模型文档

## 数据库表结构

### job_info (任务信息表)

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| job_id | BIGINT | 是 | 任务 ID，主键 |
| job_name | VARCHAR(100) | 是 | 任务名称 |
| job_group | VARCHAR(50) | 是 | 任务分组 |
| cron_expression | VARCHAR(50) | 是 | Cron 表达式 |
| job_type | VARCHAR(20) | 是 | 任务类型 |
| job_config | TEXT | 否 | 任务配置 JSON |
| status | TINYINT | 是 | 状态：0-停止，1-运行 |
| create_time | DATETIME | 是 | 创建时间 |
| update_time | DATETIME | 是 | 更新时间 |

### job_log (任务执行日志表)

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| log_id | BIGINT | 是 | 日志 ID，主键 |
| job_id | BIGINT | 是 | 任务 ID |
| execute_time | DATETIME | 是 | 执行时间 |
| status | VARCHAR(20) | 是 | 执行状态 |
| duration | BIGINT | 否 | 执行时长 (ms) |
| result | TEXT | 否 | 执行结果 |
| error_msg | TEXT | 否 | 错误信息 |

## 索引设计

| 表名 | 索引字段 | 索引类型 | 说明 |
|------|----------|----------|------|
| job_info | job_group | 普通索引 | 按分组查询 |
| job_info | status | 普通索引 | 按状态查询 |
| job_log | job_id | 普通索引 | 按任务查询 |
| job_log | execute_time | 普通索引 | 按时间查询 |

## 数据关系

```
job_info (1) ----< (N) job_log
```

## 数据量估算

| 表名 | 日增量 | 年存量 | 说明 |
|------|--------|--------|------|
| job_info | 0 | 100-1000 | 任务配置相对稳定 |
| job_log | 10,000 | 3,650,000 | 每次执行产生日志 |

## 数据归档策略

- job_log 表保留最近 3 个月数据
- 历史数据归档到 job_log_history 表
- 定期清理超过 1 年的归档数据
