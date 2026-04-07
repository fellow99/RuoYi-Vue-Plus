# feature-101 工作流模块 - API 接口

**模块编号：** feature-101  
**模块名称：** 工作流模块  
**版本：** 5.5.3  
**最后更新：** 2026-03-13

---

## 一、接口概述

**基础路径：** `/workflow`  
**认证方式：** JWT Token (Sa-Token)  
**数据格式：** JSON

---

## 二、接口清单

### 2.1 流程分类接口

| 接口名称 | 请求方式 | 接口路径 | 权限标识 | 说明 |
|---------|---------|---------|---------|------|
| 分类列表 | GET | `/workflow/category/list` | `workflow:category:list` | 查询分类列表 |
| 分类详情 | GET | `/workflow/category/{id}` | `workflow:category:query` | 获取分类详情 |
| 新增分类 | POST | `/workflow/category` | `workflow:category:add` | 新增分类 |
| 修改分类 | PUT | `/workflow/category` | `workflow:category:edit` | 修改分类 |
| 删除分类 | DELETE | `/workflow/category/{ids}` | `workflow:category:remove` | 删除分类 |

### 2.2 流程定义接口

| 接口名称 | 请求方式 | 接口路径 | 权限标识 | 说明 |
|---------|---------|---------|---------|------|
| 定义列表 | GET | `/workflow/definition/list` | `workflow:definition:list` | 查询定义列表 |
| 未发布列表 | GET | `/workflow/definition/unPublishList` | `workflow:definition:list` | 查询未发布列表 |
| 定义详情 | GET | `/workflow/definition/{id}` | `workflow:definition:query` | 获取定义详情 |
| 导入定义 | POST | `/workflow/definition/import` | `workflow:definition:import` | 导入 BPMN 文件 |
| 导出定义 | GET | `/workflow/definition/export/{id}` | `workflow:definition:export` | 导出 BPMN 文件 |
| 发布定义 | PUT | `/workflow/definition/publish/{id}` | `workflow:definition:publish` | 发布流程定义 |
| 部署定义 | PUT | `/workflow/definition/deploy/{id}` | `workflow:definition:deploy` | 部署流程定义 |
| 删除定义 | DELETE | `/workflow/definition/{ids}` | `workflow:definition:remove` | 删除定义 |

### 2.3 流程实例接口

| 接口名称 | 请求方式 | 接口路径 | 权限标识 | 说明 |
|---------|---------|---------|---------|------|
| 运行中实例 | GET | `/workflow/instance/pageByRunning` | `workflow:instance:list` | 查询运行中实例 |
| 已结束实例 | GET | `/workflow/instance/pageByFinish` | `workflow:instance:list` | 查询已结束实例 |
| 实例详情 | GET | `/workflow/instance/getInfo/{businessId}` | `workflow:instance:query` | 获取实例详情 |
| 删除实例 | DELETE | `/workflow/instance/deleteByBusinessIds/{ids}` | `workflow:instance:remove` | 删除实例 |
| 取消实例 | PUT | `/workflow/instance/cancel` | `workflow:instance:cancel` | 取消实例 |
| 作废实例 | PUT | `/workflow/instance/invalid` | `workflow:instance:invalid` | 作废实例 |

### 2.4 任务管理接口

| 接口名称 | 请求方式 | 接口路径 | 权限标识 | 说明 |
|---------|---------|---------|---------|------|
| 待办任务 | GET | `/workflow/task/pageByTaskWait` | `workflow:task:list` | 查询待办任务 |
| 已办任务 | GET | `/workflow/task/pageByTaskFinish` | `workflow:task:list` | 查询已办任务 |
| 启动流程 | POST | `/workflow/task/startWorkFlow` | `workflow:task:start` | 启动流程 |
| 办理任务 | POST | `/workflow/task/completeTask` | `workflow:task:complete` | 办理任务 |
| 委派任务 | POST | `/workflow/task/delegateTask` | `workflow:task:delegate` | 委派任务 |
| 转办任务 | POST | `/workflow/task/transferTask` | `workflow:task:transfer` | 转办任务 |
| 催办任务 | POST | `/workflow/task/urgeTask` | `workflow:task:urge` | 催办任务 |
| 回退任务 | POST | `/workflow/task/backTask` | `workflow:task:back` | 回退任务 |
| 驳回任务 | POST | `/workflow/task/rejectTask` | `workflow:task:reject` | 驳回任务 |
| 历史任务 | GET | `/workflow/task/pageByHistory` | `workflow:task:list` | 查询历史任务 |

### 2.5 流程变量接口

| 接口名称 | 请求方式 | 接口路径 | 权限标识 | 说明 |
|---------|---------|---------|---------|------|
| 设置变量 | POST | `/workflow/variable/set` | `workflow:variable:set` | 设置变量 |
| 查询变量 | GET | `/workflow/variable/get/{instanceId}` | `workflow:variable:query` | 查询变量 |
| 删除变量 | DELETE | `/workflow/variable/remove` | `workflow:variable:remove` | 删除变量 |

### 2.6 SPeL 表达式接口

| 接口名称 | 请求方式 | 接口路径 | 权限标识 | 说明 |
|---------|---------|---------|---------|------|
| 表达式列表 | GET | `/workflow/spel/list` | `workflow:spel:list` | 查询表达式列表 |
| 表达式详情 | GET | `/workflow/spel/{id}` | `workflow:spel:query` | 获取表达式详情 |
| 新增表达式 | POST | `/workflow/spel` | `workflow:spel:add` | 新增表达式 |
| 修改表达式 | PUT | `/workflow/spel` | `workflow:spel:edit` | 修改表达式 |
| 删除表达式 | DELETE | `/workflow/spel/{ids}` | `workflow:spel:remove` | 删除表达式 |
| 测试表达式 | POST | `/workflow/spel/test` | `workflow:spel:test` | 测试表达式 |

---

## 三、接口详细定义

### 3.1 查询流程定义列表

**接口：** `GET /workflow/definition/list`

**权限：** `workflow:definition:list`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| name | String | 否 | 流程名称（模糊匹配） |
| key | String | 否 | 流程标识 |
| categoryId | Long | 否 | 分类 ID |
| status | Integer | 否 | 状态（0-未发布 1-已发布） |
| pageNum | Integer | 否 | 页码（默认 1） |
| pageSize | Integer | 否 | 每页数量（默认 10） |

**响应示例：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "rows": [
    {
      "id": 1,
      "name": "请假流程",
      "key": "leave_flow",
      "version": 1,
      "categoryName": "人事流程",
      "status": 1,
      "createTime": "2026-03-13 10:00:00"
    }
  ],
  "total": 1
}
```

---

### 3.2 启动流程

**接口：** `POST /workflow/task/startWorkFlow`

**权限：** `workflow:task:start`

**请求体：**

```json
{
  "definitionKey": "leave_flow",
  "businessId": 1001,
  "title": "张三的请假申请",
  "variables": {
    "days": 3,
    "reason": "事假"
  }
}
```

**响应示例：**

```json
{
  "code": 200,
  "msg": "提交成功",
  "data": {
    "instanceId": "INS_202603130001",
    "processName": "请假流程",
    "taskId": "TASK_202603130001",
    "nodeName": "部门经理审批"
  }
}
```

---

### 3.3 办理任务

**接口：** `POST /workflow/task/completeTask`

**权限：** `workflow:task:complete`

**请求体：**

```json
{
  "taskId": "TASK_202603130001",
  "comment": "同意",
  "variables": {
    "approved": true
  }
}
```

**响应示例：**

```json
{
  "code": 200,
  "msg": "操作成功"
}
```

---

### 3.4 查询待办任务

**接口：** `GET /workflow/task/pageByTaskWait`

**权限：** `workflow:task:list`

**请求参数：**

| 参数名 | 类型 | 必填 | 说明 |
|-------|------|------|------|
| processName | String | 否 | 流程名称 |
| taskName | String | 否 | 任务名称 |
| pageNum | Integer | 否 | 页码 |
| pageSize | Integer | 否 | 每页数量 |

**响应示例：**

```json
{
  "code": 200,
  "msg": "查询成功",
  "rows": [
    {
      "taskId": "TASK_202603130001",
      "instanceId": "INS_202603130001",
      "processName": "请假流程",
      "taskName": "部门经理审批",
      "nodeName": "经理审批节点",
      "assignee": 2,
      "assigneeName": "李四",
      "priority": 0,
      "arriveTime": "2026-03-13 10:00:00"
    }
  ],
  "total": 1
}
```

---

**最后更新：** 2026-03-13  
**文档状态：** ✅ 完成
