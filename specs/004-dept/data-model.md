# 004-部门管理 - 数据模型

**模块编号：** 004  
**模块名称：** 部门管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 部门表 (sys_dept)

**表名：** `sys_dept`

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| dept_id | BIGINT | - | YES | - | 部门 ID（主键） |
| tenant_id | VARCHAR | 20 | NO | '000000' | 租户编号 |
| parent_id | BIGINT | - | NO | 0 | 父部门 ID |
| ancestors | VARCHAR | 500 | NO | - | 祖级列表（祖部门 ID 路径） |
| dept_name | VARCHAR | 30 | YES | - | 部门名称 |
| order_num | INT | - | NO | 0 | 显示顺序 |
| leader | BIGINT | - | NO | NULL | 负责人（用户 ID） |
| phone | VARCHAR | 11 | NO | - | 联系电话 |
| email | VARCHAR | 50 | NO | - | 邮箱 |
| status | CHAR | 1 | NO | '0' | 状态（0 正常 1 停用） |
| del_flag | CHAR | 1 | NO | '0' | 删除标志 |
| create_by | BIGINT | - | NO | NULL | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | BIGINT | - | NO | NULL | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |

**索引：** PRIMARY KEY (`dept_id`), KEY `idx_parent_id` (`parent_id`)

**约束：** `parent_id` + `dept_name` 联合唯一

**ancestors 字段说明：**
- 存储从根部门到当前部门的所有祖先 ID
- 格式：`100,101,102`（逗号分隔）
- 用于快速查询上级/下级部门

---

## 二、数据对象类

### 2.1 SysDept (DO)

```java
@TableName("sys_dept")
public class SysDept extends TenantEntity {
    @TableId(value = "dept_id")
    private Long deptId;
    private Long parentId;
    private String ancestors;
    private String deptName;
    private Integer orderNum;
    private Long leader;
    private String phone;
    private String email;
    private String status;
    @TableLogic
    private String delFlag;
}
```

### 2.2 SysDeptBo (BO)

```java
@AutoMapper(target = SysDept.class)
public class SysDeptBo extends BaseEntity {
    private Long deptId;
    private Long parentId;
    
    @NotBlank(message = "部门名称不能为空")
    @Size(min = 0, max = 30, message = "部门名称长度不能超过{max}个字符")
    private String deptName;
    
    private Integer orderNum;
    private Long leader;
    private String phone;
    private String email;
    private String status;
}
```

### 2.3 SysDeptVo (VO)

```java
@AutoMapper(target = SysDept.class)
public class SysDeptVo implements Serializable {
    private Long deptId;
    private Long parentId;
    private String ancestors;
    private String deptName;
    private Integer orderNum;
    private String leaderName;  // 负责人姓名
    private String phone;
    private String email;
    private String status;
    private Date createTime;
    private List<SysDeptVo> children;  // 子部门
}
```

---

## 三、数据状态说明

### 3.1 部门状态 (status)

| 值 | 说明 |
|---|------|
| 0 | 正常 |
| 1 | 停用 |

### 3.2 删除标志 (delFlag)

| 值 | 说明 |
|---|------|
| 0 | 存在 |
| 1 | 删除 |

---

## 四、树形结构说明

### 4.1 父部门 ID (parentId)

| 值 | 说明 |
|---|------|
| 0 | 根部门 |
| 其他 | 子部门 |

### 4.2 祖级列表 (ancestors)

示例：
- 根部门 A（dept_id=100）：`ancestors = "100"`
- 子部门 B（dept_id=101，parent_id=100）：`ancestors = "100,101"`
- 孙部门 C（dept_id=102，parent_id=101）：`ancestors = "100,101,102"`

**用途：**
- 快速查询所有上级部门
- 快速查询所有下级部门（LIKE '100,%'）
- 数据权限过滤

