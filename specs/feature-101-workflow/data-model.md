# feature-101 工作流模块 - 数据模型

**模块编号：** feature-101  
**模块名称：** 工作流模块  
**版本：** 5.5.3 (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 流程分类表 (flw_category)

**表名：** `flw_category`  
**说明：** 存储流程定义分类信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| category_id | BIGINT | - | YES | - | 分类 ID（主键） |
| category_name | VARCHAR | 100 | YES | - | 分类名称 |
| category_code | VARCHAR | 50 | YES | - | 分类编码 |
| sort | INT | - | NO | 0 | 排序 |
| status | CHAR | 1 | NO | 0 | 状态（0 正常 1 停用） |
| remark | VARCHAR | 500 | NO | - | 备注 |
| create_by | VARCHAR | 64 | NO | - | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | VARCHAR | 64 | NO | - | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |
| tenant_id | VARCHAR | 20 | NO | - | 租户 ID |
| del_flag | CHAR | 1 | NO | 0 | 删除标志 |

**索引：**
- PRIMARY KEY (`category_id`)
- UNIQUE KEY `uk_category_code` (`category_code`)
- KEY `idx_status` (`status`)

---

### 1.2 流程定义表 (flw_definition)

**表名：** `flw_definition`  
**说明：** 存储流程定义信息（Warm-Flow 引擎表）

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| id | BIGINT | - | YES | - | 定义 ID（主键） |
| name | VARCHAR | 255 | YES | - | 流程名称 |
| key | VARCHAR | 255 | YES | - | 流程标识 |
| version | INT | - | YES | 1 | 版本号 |
| category_id | BIGINT | - | NO | NULL | 分类 ID |
| bpmn_xml | TEXT | - | NO | NULL | BPMN XML 内容 |
| form_key | VARCHAR | 255 | NO | NULL | 表单 Key |
| status | INT | - | NO | 0 | 状态（0-未发布 1-已发布） |
| main_version | TINYINT | - | NO | 0 | 是否主版本 |
| description | VARCHAR | 500 | NO | - | 描述 |
| create_by | VARCHAR | 64 | NO | - | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| tenant_id | VARCHAR | 20 | NO | - | 租户 ID |

**索引：**
- PRIMARY KEY (`id`)
- UNIQUE KEY `uk_key_version` (`key`, `version`)
- KEY `idx_category_id` (`category_id`)
- KEY `idx_status` (`status`)

---

### 1.3 流程实例表 (flw_instance)

**表名：** `flw_instance`  
**说明：** 存储流程实例信息（Warm-Flow 引擎表）

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| id | BIGINT | - | YES | - | 实例 ID（主键） |
| definition_id | BIGINT | - | YES | - | 流程定义 ID |
| business_id | BIGINT | - | YES | - | 业务 ID |
| title | VARCHAR | 255 | NO | - | 实例标题 |
| start_user_id | BIGINT | - | YES | - | 发起人 ID |
| start_time | DATETIME | - | YES | - | 启动时间 |
| end_time | DATETIME | - | NO | NULL | 结束时间 |
| status | INT | - | NO | 0 | 状态（0-运行中 1-已结束 2-已取消） |
| variables | TEXT | - | NO | NULL | 流程变量（JSON） |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| tenant_id | VARCHAR | 20 | NO | - | 租户 ID |

**索引：**
- PRIMARY KEY (`id`)
- KEY `idx_definition_id` (`definition_id`)
- KEY `idx_business_id` (`business_id`)
- KEY `idx_start_user_id` (`start_user_id`)
- KEY `idx_status` (`status`)

---

### 1.4 流程任务表 (flw_task)

**表名：** `flw_task`  
**说明：** 存储流程任务信息（Warm-Flow 引擎表）

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| id | BIGINT | - | YES | - | 任务 ID（主键） |
| instance_id | BIGINT | - | YES | - | 流程实例 ID |
| definition_id | BIGINT | - | YES | - | 流程定义 ID |
| node_id | VARCHAR | 255 | YES | - | 节点 ID |
| node_name | VARCHAR | 255 | YES | - | 节点名称 |
| task_name | VARCHAR | 255 | NO | - | 任务名称 |
| assignee | BIGINT | - | NO | NULL | 处理人 ID |
| candidate_users | TEXT | - | NO | NULL | 候选用户（JSON） |
| candidate_roles | TEXT | - | NO | NULL | 候选角色（JSON） |
| status | INT | - | NO | 0 | 状态（0-待办 1-已办 2-已取消） |
| priority | INT | - | NO | 0 | 优先级 |
| arrive_time | DATETIME | - | YES | - | 到达时间 |
| finish_time | DATETIME | - | NO | NULL | 完成时间 |
| comment | VARCHAR | 500 | NO | - | 办理意见 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| tenant_id | VARCHAR | 20 | NO | - | 租户 ID |

**索引：**
- PRIMARY KEY (`id`)
- KEY `idx_instance_id` (`instance_id`)
- KEY `idx_assignee` (`assignee`)
- KEY `idx_status` (`status`)
- KEY `idx_arrive_time` (`arrive_time`)

---

### 1.5 流程历史任务表 (flw_his_task)

**表名：** `flw_his_task`  
**说明：** 存储流程历史任务信息

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| id | BIGINT | - | YES | - | 历史记录 ID（主键） |
| task_id | BIGINT | - | YES | - | 任务 ID |
| instance_id | BIGINT | - | YES | - | 流程实例 ID |
| node_name | VARCHAR | 255 | YES | - | 节点名称 |
| assignee | BIGINT | - | NO | NULL | 处理人 ID |
| assignee_name | VARCHAR | 100 | NO | - | 处理人姓名 |
| status | INT | - | NO | 0 | 状态 |
| comment | VARCHAR | 500 | NO | - | 办理意见 |
| arrive_time | DATETIME | - | YES | - | 到达时间 |
| finish_time | DATETIME | - | NO | NULL | 完成时间 |
| duration | BIGINT | - | NO | 0 | 耗时（毫秒） |
| tenant_id | VARCHAR | 20 | NO | - | 租户 ID |

**索引：**
- PRIMARY KEY (`id`)
- KEY `idx_task_id` (`task_id`)
- KEY `idx_instance_id` (`instance_id`)
- KEY `idx_assignee` (`assignee`)

---

### 1.6 流程 SPeL 表达式表 (flw_spel)

**表名：** `flw_spel`  
**说明：** 存储流程中使用的 SPeL 表达式

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| spel_id | BIGINT | - | YES | - | 表达式 ID（主键） |
| spel_name | VARCHAR | 100 | YES | - | 表达式名称 |
| spel_content | TEXT | - | YES | - | 表达式内容 |
| description | VARCHAR | 500 | NO | - | 描述 |
| create_by | VARCHAR | 64 | NO | - | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| tenant_id | VARCHAR | 20 | NO | - | 租户 ID |

**索引：**
- PRIMARY KEY (`spel_id`)
- KEY `idx_spel_name` (`spel_name`)

---

## 二、Warm-Flow 引擎表

Warm-Flow 引擎使用以下核心表（由引擎自动管理）：

- `warm_flow_def` - 流程定义表
- `warm_flow_ins` - 流程实例表
- `warm_flow_task` - 流程任务表
- `warm_flow_data` - 流程数据表
- `warm_flow_link` - 流程链接表

---

## 三、关联实体

### 3.1 用户实体 (SysUser)

流程实例和任务通过用户 ID 关联用户实体。

**关联字段：**
- `start_user_id` - 流程发起人
- `assignee` - 任务处理人

### 3.2 部门实体 (SysDept)

通过用户关联部门，用于数据权限控制。

---

## 四、数据模型类图

```
┌─────────────────┐       ┌──────────────────┐
│  FlwCategory    │       │ FlwDefinition    │
├─────────────────┤       ├──────────────────┤
│ - categoryId    │◀──────│ - categoryId     │
│ - categoryName  │       │ - id             │
│ - categoryCode  │       │ - name           │
│ - sort          │       │ - key            │
│ - status        │       │ - version        │
└─────────────────┘       │ - bpmnXml        │
                          │ - status         │
                          └────────┬─────────┘
                                   │ 1:N
                                   ▼
                          ┌──────────────────┐
                          │  FlwInstance     │
                          ├──────────────────┤
                          │ - id             │
                          │ - definitionId   │
                          │ - businessId     │
                          │ - startUserId    │
                          │ - status         │
                          └────────┬─────────┘
                                   │ 1:N
                                   ▼
                          ┌──────────────────┐
                          │    FlwTask       │
                          ├──────────────────┤
                          │ - id             │
                          │ - instanceId     │
                          │ - assignee       │
                          │ - status         │
                          │ - comment        │
                          └──────────────────┘
```

---

**最后更新：** 2026-03-13  
**文档状态：** ✅ 完成
