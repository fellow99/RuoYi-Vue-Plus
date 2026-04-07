# 005-岗位管理 - 数据模型

**模块编号：** 005  
**模块名称：** 岗位管理  
**版本：** 5.X (RuoYi-Vue-Plus)  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 岗位表 (sys_post)

**表名：** `sys_post`

| 字段名 | 类型 | 长度 | 必填 | 默认值 | 说明 |
|-------|------|------|------|--------|------|
| post_id | BIGINT | - | YES | - | 岗位 ID（主键） |
| tenant_id | VARCHAR | 20 | NO | '000000' | 租户编号 |
| post_code | VARCHAR | 64 | YES | - | 岗位编码 |
| post_name | VARCHAR | 50 | YES | - | 岗位名称 |
| post_sort | INT | - | YES | - | 显示顺序 |
| status | CHAR | 1 | NO | '0' | 状态（0 正常 1 停用） |
| del_flag | CHAR | 1 | NO | '0' | 删除标志 |
| create_by | BIGINT | - | NO | NULL | 创建者 |
| create_time | DATETIME | - | NO | NULL | 创建时间 |
| update_by | BIGINT | - | NO | NULL | 更新者 |
| update_time | DATETIME | - | NO | NULL | 更新时间 |
| remark | VARCHAR | 500 | NO | - | 备注 |

**索引：** PRIMARY KEY (`post_id`), UNIQUE KEY `uk_post_code` (`post_code`)

**约束：**
- `post_code` 唯一
- `post_name` 唯一

### 1.2 用户岗位关联表 (sys_user_post)

| 字段名 | 类型 | 说明 |
|-------|------|------|
| user_id | BIGINT | 用户 ID |
| post_id | BIGINT | 岗位 ID |

---

## 二、数据对象类

### 2.1 SysPost (DO)

```java
@TableName("sys_post")
public class SysPost extends TenantEntity {
    @TableId(value = "post_id")
    private Long postId;
    private String postCode;
    private String postName;
    private Integer postSort;
    private String status;
    @TableLogic
    private String delFlag;
}
```

### 2.2 SysPostBo (BO)

```java
@AutoMapper(target = SysPost.class)
public class SysPostBo extends BaseEntity {
    private Long postId;
    
    @NotBlank(message = "岗位编码不能为空")
    @Size(min = 0, max = 64, message = "岗位编码长度不能超过{max}个字符")
    private String postCode;
    
    @NotBlank(message = "岗位名称不能为空")
    @Size(min = 0, max = 50, message = "岗位名称长度不能超过{max}个字符")
    private String postName;
    
    private Integer postSort;
    private String status;
}
```

### 2.3 SysPostVo (VO)

```java
@AutoMapper(target = SysPost.class)
public class SysPostVo implements Serializable {
    private Long postId;
    private String postCode;
    private String postName;
    private Integer postSort;
    private String status;
    private Date createTime;
}
```

---

## 三、数据状态说明

### 3.1 岗位状态 (status)

| 值 | 说明 |
|---|------|
| 0 | 正常 |
| 1 | 停用 |

### 3.2 删除标志 (delFlag)

| 值 | 说明 |
|---|------|
| 0 | 存在 |
| 1 | 删除 |

