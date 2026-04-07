# 010-操作日志 - API 接口

**模块编号：** 010  
**最后更新：** 2026-03-13

---

## 接口列表

### 1.1 获取操作日志列表

**接口：** `GET /monitor/operlog/list`

**权限：** `monitor:operlog:list`

**参数：** title, businessType, operName, status, operTime, pageNum, pageSize

### 1.2 导出操作日志

**接口：** `POST /monitor/operlog/export`

**权限：** `monitor:operlog:export`

**参数：** 同列表查询

**响应：** Excel 文件下载

### 1.3 删除操作日志

**接口：** `DELETE /monitor/operlog/{operIds}`

**权限：** `monitor:operlog:remove`

**参数：** operIds - 日志 ID 列表

### 1.4 清空操作日志

**接口：** `DELETE /monitor/operlog/clean`

**权限：** `monitor:operlog:remove`

**说明：** 清空所有操作日志

