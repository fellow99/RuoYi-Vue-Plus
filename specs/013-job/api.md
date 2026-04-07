# 013-job API 接口文档

## 接口列表

| 接口 | 方法 | 权限 | 说明 |
|------|------|------|------|
| /job/info/list | GET | monitor:job:list | 查询任务列表 |
| /job/info | POST | monitor:job:add | 创建任务 |
| /job/info | PUT | monitor:job:edit | 更新任务 |
| /job/info/{jobId} | DELETE | monitor:job:delete | 删除任务 |
| /job/info/{jobId}/start | POST | monitor:job:start | 启动任务 |
| /job/info/{jobId}/stop | POST | monitor:job:stop | 停止任务 |
| /job/info/{jobId}/trigger | POST | monitor:job:list | 手动触发 |
| /job/log/list | GET | monitor:job:list | 查询执行日志 |

## 接口详情

### 1. 查询任务列表

**接口**: `GET /job/info/list`

**权限**: `monitor:job:list`

**请求参数**:
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| jobName | String | 否 | 任务名称 |
| jobGroup | String | 否 | 任务分组 |
| status | Integer | 否 | 状态 |
| pageNum | Integer | 否 | 页码 |
| pageSize | Integer | 否 | 每页条数 |

**响应**:
```json
{
  "code": 200,
  "data": {
    "total": 10,
    "rows": [{
      "jobId": 1,
      "jobName": "test-job",
      "jobGroup": "default",
      "cronExpression": "0/5 * * * * ?",
      "status": 1
    }]
  }
}
```

### 2. 创建任务

**接口**: `POST /job/info`

**权限**: `monitor:job:add`

**请求体**:
```json
{
  "jobName": "test-job",
  "jobGroup": "default",
  "cronExpression": "0/5 * * * * ?",
  "jobType": "JAVA",
  "jobConfig": "{}"
}
```

### 3. 更新任务

**接口**: `PUT /job/info`

**权限**: `monitor:job:edit`

### 4. 删除任务

**接口**: `DELETE /job/info/{jobId}`

**权限**: `monitor:job:delete`

### 5. 启动任务

**接口**: `POST /job/info/{jobId}/start`

**权限**: `monitor:job:start`

### 6. 停止任务

**接口**: `POST /job/info/{jobId}/stop`

**权限**: `monitor:job:stop`

### 7. 手动触发

**接口**: `POST /job/info/{jobId}/trigger`

**权限**: `monitor:job:list`

### 8. 查询执行日志

**接口**: `GET /job/log/list`

**权限**: `monitor:job:list`

**请求参数**:
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| jobId | Long | 否 | 任务 ID |
| status | String | 否 | 执行状态 |
| startTime | Date | 否 | 开始时间 |
| endTime | Date | 否 | 结束时间 |
