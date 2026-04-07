# 011-登录日志 - API 接口

**模块编号：** 011  
**最后更新：** 2026-03-13

---

## 接口列表

### 1.1 获取登录日志列表

**接口：** `GET /monitor/logininfor/list`

**权限：** `monitor:logininfor:list`

**参数：** userName, ipaddr, status, loginTime, pageNum, pageSize

### 1.2 导出登录日志

**接口：** `POST /monitor/logininfor/export`

**权限：** `monitor:logininfor:export`

**参数：** 同列表查询

**响应：** Excel 文件下载

### 1.3 删除登录日志

**接口：** `DELETE /monitor/logininfor/{infoIds}`

**权限：** `monitor:logininfor:remove`

**参数：** infoIds - 日志 ID 列表

### 1.4 清空登录日志

**接口：** `DELETE /monitor/logininfor/clean`

**权限：** `monitor:logininfor:remove`

**说明：** 清空所有登录日志

### 1.5 解锁账户

**接口：** `GET /monitor/logininfor/unlock/{userName}`

**权限：** `monitor:logininfor:unlock`

**说明：** 解锁因密码错误被锁定的账户

**逻辑：** 删除 Redis 键 `pwd_err_cnt:{userName}`

