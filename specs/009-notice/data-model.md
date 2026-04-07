# 009-通知公告 - 数据模型

**模块编号：** 009  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 通知公告表 (sys_notice)

| 字段名 | 类型 | 长度 | 必填 | 说明 |
|-------|------|------|------|------|
| notice_id | BIGINT | - | YES | 公告 ID |
| notice_title | VARCHAR | 50 | YES | 公告标题 |
| notice_type | CHAR | 1 | YES | 公告类型（1 通知 2 公告） |
| notice_content | LONGTEXT | - | YES | 公告内容（富文本） |
| status | CHAR | 1 | NO | 状态（0 正常 1 关闭） |
| remark | VARCHAR | 500 | NO | 备注 |
| tenant_id | VARCHAR | 20 | NO | 租户 ID |
| create_by/create_time/update_by/update_time | - | - | NO | 审计字段 |

**索引：** PRIMARY KEY (notice_id)

---

## 二、数据对象类

### 2.1 SysNotice (DO)

```java
@Data
@TableName("sys_notice")
public class SysNotice extends TenantEntity {
    @TableId(value = "notice_id")
    private Long noticeId;
    private String noticeTitle;
    private String noticeType;
    private String noticeContent;
    private String status;
    private String remark;
}
```

### 2.2 SysNoticeBo (BO)

```java
@AutoMapper(target = SysNotice.class)
public class SysNoticeBo extends BaseEntity {
    @NotBlank(message = "公告标题不能为空")
    private String noticeTitle;
    
    @NotBlank(message = "公告类型不能为空")
    private String noticeType;
    
    @NotBlank(message = "公告内容不能为空")
    private String noticeContent;
    
    private String status;
    private String remark;
}
```

### 2.3 SysNoticeVo (VO)

```java
@AutoMapper(target = SysNotice.class)
public class SysNoticeVo implements Serializable {
    private Long noticeId;
    private String noticeTitle;
    private String noticeType;
    private String noticeContent;
    private String status;
    private String remark;
}
```

