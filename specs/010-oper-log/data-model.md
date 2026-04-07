# 010-操作日志 - 数据模型

**模块编号：** 010  
**最后更新：** 2026-03-13

---

## 一、核心数据表

### 1.1 操作日志表 (sys_oper_log)

| 字段名 | 类型 | 长度 | 必填 | 说明 |
|-------|------|------|------|------|
| oper_id | BIGINT | - | YES | 日志主键 |
| tenant_id | VARCHAR | 20 | NO | 租户 ID |
| title | VARCHAR | 50 | NO | 操作模块 |
| business_type | INT | - | NO | 业务类型（0-9） |
| method | VARCHAR | 100 | NO | 请求方法 |
| request_method | VARCHAR | 10 | NO | 请求方式（GET/POST 等） |
| operator_type | INT | - | NO | 操作类别（0 其他 1 后台 2 手机） |
| oper_name | VARCHAR | 50 | NO | 操作人员 |
| dept_name | VARCHAR | 50 | NO | 部门名称 |
| oper_url | VARCHAR | 255 | NO | 请求 URL |
| oper_ip | VARCHAR | 128 | NO | 操作地址 |
| oper_location | VARCHAR | 255 | NO | 操作地点 |
| oper_param | VARCHAR | 2000 | NO | 请求参数 |
| json_result | VARCHAR | 2000 | NO | 返回参数 |
| status | INT | - | NO | 操作状态（0 正常 1 异常） |
| error_msg | VARCHAR | 2000 | NO | 错误消息 |
| oper_time | DATETIME | - | NO | 操作时间 |
| cost_time | BIGINT | - | NO | 消耗时间（毫秒） |

**索引：** PRIMARY KEY (oper_id), KEY idx_oper_time (oper_time), KEY idx_oper_name (oper_name)

---

## 二、数据对象类

### 2.1 SysOperLog (DO)

```java
@Data
@TableName("sys_oper_log")
public class SysOperLog implements Serializable {
    @TableId(value = "oper_id")
    private Long operId;
    private String tenantId;
    private String title;
    private Integer businessType;
    private String method;
    private String requestMethod;
    private Integer operatorType;
    private String operName;
    private String deptName;
    private String operUrl;
    private String operIp;
    private String operLocation;
    private String operParam;
    private String jsonResult;
    private Integer status;
    private String errorMsg;
    private Date operTime;
    private Long costTime;
}
```

### 2.2 SysOperLogVo (VO)

```java
@AutoMapper(target = SysOperLog.class)
public class SysOperLogVo implements Serializable {
    private Long operId;
    private String title;
    private Integer businessType;
    private String method;
    private String requestMethod;
    private String operName;
    private String deptName;
    private String operUrl;
    private String operIp;
    private String operLocation;
    private Integer status;
    private String errorMsg;
    private Date operTime;
    private Long costTime;
}
```

