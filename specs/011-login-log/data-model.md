# 011-登录日志 - 数据模型

**模块编号：** 011  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 登录日志表 (sys_logininfor)

| 字段名 | 类型 | 长度 | 必填 | 说明 |
|-------|------|------|------|------|
| info_id | BIGINT | - | YES | 日志主键 |
| tenant_id | VARCHAR | 20 | NO | 租户 ID |
| user_name | VARCHAR | 50 | NO | 用户账号 |
| client_key | VARCHAR | 32 | NO | 客户端（如 default） |
| device_type | VARCHAR | 32 | NO | 设备类型（如 pc） |
| status | CHAR | 1 | NO | 登录状态（0 成功 1 失败） |
| ipaddr | VARCHAR | 128 | NO | 登录 IP 地址 |
| login_location | VARCHAR | 255 | NO | 登录地点 |
| browser | VARCHAR | 50 | NO | 浏览器类型 |
| os | VARCHAR | 50 | NO | 操作系统 |
| msg | VARCHAR | 255 | NO | 提示消息 |
| login_time | DATETIME | - | NO | 访问时间 |

**索引：** PRIMARY KEY (info_id), KEY idx_user_name (user_name), KEY idx_login_time (login_time)

---

## 二、数据对象类

### 2.1 SysLogininfor (DO)

```java
@Data
@TableName("sys_logininfor")
public class SysLogininfor implements Serializable {
    @TableId(value = "info_id")
    private Long infoId;
    private String tenantId;
    private String userName;
    private String clientKey;
    private String deviceType;
    private String status;
    private String ipaddr;
    private String loginLocation;
    private String browser;
    private String os;
    private String msg;
    private Date loginTime;
}
```

### 2.2 SysLogininforVo (VO)

```java
@AutoMapper(target = SysLogininfor.class)
public class SysLogininforVo implements Serializable {
    private Long infoId;
    private String userName;
    private String clientKey;
    private String deviceType;
    private String status;
    private String ipaddr;
    private String loginLocation;
    private String browser;
    private String os;
    private String msg;
    private Date loginTime;
}
```

