# 009-通知公告 - API 接口

**模块编号：** 009  
**最后更新：** 2026-03-13

---

## 接口列表

### 1.1 获取公告列表

**接口：** `GET /system/notice/list`

**权限：** `system:notice:list`

**参数：** noticeTitle, noticeType, status, pageNum, pageSize

### 1.2 获取公告详情

**接口：** `GET /system/notice/{noticeId}`

**权限：** `system:notice:query`

### 1.3 新增公告

**接口：** `POST /system/notice`

**权限：** `system:notice:add`

**参数：** noticeTitle, noticeType, noticeContent, status, remark

**响应：** 成功后通过 SSE 推送

### 1.4 修改公告

**接口：** `PUT /system/notice`

**权限：** `system:notice:edit`

### 1.5 删除公告

**接口：** `DELETE /system/notice/{noticeIds}`

**权限：** `system:notice:remove`

