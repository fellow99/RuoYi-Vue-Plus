# 009-notice 技术方案 (plan.md)

> 对应规格：spec.md | 模块：009-notice | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17

### 1.2 依赖
- ruoyi-common-core（R 响应、DictService 字典接口）
- ruoyi-common-mybatis（BaseMapperPlus、分页）
- ruoyi-common-security（Sa-Token 权限）
- ruoyi-common-log（@Log 操作日志）
- ruoyi-common-redis（@RepeatSubmit 防重复提交）
- ruoyi-api（MessageService 消息推送、PushPayloadDTO 推送载体）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → Service(接口+实现) → Mapper → Entity |
| 注解驱动 | ✅ | @SaCheckPermission 权限控制 |
| 消息推送 | ✅ | 通过 api 层的 MessageService 接口解耦，支持 WebSocket + SSE |

## 3. 数据模型

### 3.1 核心表
- `sys_notice` — 通知公告表（noticeId 主键）

### 3.2 关键字段规则
- `noticeTitle`: 公告标题
- `noticeType`: 1=通知, 2=公告（对应字典 `sys_notice_type`）
- `noticeContent`: 公告内容（富文本）
- `status`: 0=正常（展示）, 1=关闭（隐藏）
- 继承 BaseEntity：createBy, createTime, updateBy, updateTime, remark

## 4. 接口契约

### 4.1 提供接口

**SysNoticeController** (`/system/notice`):

| 方法 | 路径 | 权限 | 说明 |
|------|------|------|------|
| GET | `/list` | system:notice:list | 分页查询通知公告（支持按创建人筛选） |
| GET | `/{noticeId}` | system:notice:query | 查询公告详情 |
| POST | `/` | system:notice:add | 新增通知公告 → 自动推送在线用户 |
| PUT | `/` | system:notice:edit | 修改通知公告（不推送） |
| DELETE | `/{noticeIds}` | system:notice:remove | 批量删除通知公告 |

### 4.2 消费接口
- `DictService.getDictLabel("sys_notice_type", noticeType)` — 获取公告类型中文标签
- `MessageService.publishAll(PushPayloadDTO)` — 向所有在线用户推送公告

### 4.3 推送消息格式
```json
{
  "pushType": "NOTICE",
  "source": "NOTICE",
  "title": "[通知] 系统维护通知",
  "data": {
    "noticeType": "1",
    "noticeTypeLabel": "通知",
    "noticeTitle": "系统维护通知",
    "noticeId": 123,
    "noticeContent": "...",
    "status": "0"
  },
  "url": "/system/notice?noticeId=123"
}
```

## 5. 实现策略

### 5.1 架构模式
标准四层 CRUD + 消息推送：

```
SysNoticeController(/system/notice) → ISysNoticeService/SysNoticeServiceImpl → SysNoticeMapper → MySQL
                                       ↓
                                  insertNotice() 成功后
                                       ↓
                              Controller.add() 中调用
                                       ↓
                          messageService.publishAll(PushPayloadDTO)
                                       ↓
                        WebSocket / SSE → 所有在线用户
```

### 5.2 关键算法
- **公告发布流程**：
  1. Controller.add() → noticeService.insertNotice() 持久化
  2. dictService.getDictLabel("sys_notice_type", noticeType) 获取类型标签
  3. 构造 PushPayloadDTO（PushTypeEnum.NOTICE, PushSourceEnum.NOTICE）
  4. messageService.publishAll() 广播
- **按创建人搜索**：通过 `SysUserMapper` 根据 userName 查询 userId，再按 createBy 筛选公告
- **防重复提交**：新增/修改接口标注 `@RepeatSubmit()`

### 5.3 错误处理
- insertNotice() 返回行数 ≤ 0 → R.fail()
- 全局异常转 R 响应

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysNoticeController | 通知公告 REST API（含推送逻辑） | system/controller/system/ |
| ISysNoticeService | 通知公告业务接口 | system/service/ |
| SysNoticeServiceImpl | 通知公告业务实现 | system/service/impl/ |
| SysNoticeMapper | 通知公告数据访问 | system/mapper/ |
| SysNotice.java | 通知公告实体 | system/domain/ |
| SysNoticeBo.java | 通知公告请求体 | system/domain/bo/ |
| SysNoticeVo.java | 通知公告响应体 | system/domain/vo/ |
