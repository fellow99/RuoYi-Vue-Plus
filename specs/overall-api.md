# 整体接口模型 (overall-api.md)

**版本：** 6.0.0  
**最后更新：** 2026-08-10  
**项目：** RuoYi-Vue-Plus

> 本文档为系统级 API 清单。各模块详细 API 定义见对应模块的 `spec.md` 和 `plan.md`。

---

## 一、API 规范

### 1.1 认证机制
- **认证方式：** Sa-Token + JWT，请求头 `Authorization: Bearer <token>`
- **公开接口：** 标注 `@SaIgnore` 的接口无需认证（登录、注册、验证码等）
- **权限控制：** `@SaCheckRole`, `@SaCheckPermission` 注解

### 1.2 统一响应格式
```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {}
}
```

### 1.3 分页请求
```json
{
  "pageNum": 1,
  "pageSize": 10,
  "orderByColumn": "createTime",
  "isAsc": "desc"
}
```

---

## 二、API 模块分组

系统共 **51 个 REST Controller**，按模块分组如下：

| 模块组 | Controller 数量 | 说明 |
|--------|----------------|------|
| ruoyi-admin | 3 | 认证（登录/注册/验证码）、首页 |
| ruoyi-system (system) | 15 | 用户/角色/菜单/部门/岗位/字典/参数/通知/客户端/OSS/社交/消息/个人中心 |
| ruoyi-system (monitor) | 4 | 操作日志/登录日志/在线用户/缓存监控 |
| ruoyi-gen | 1 | 代码生成器 |
| ruoyi-demo | 20 | 功能示例（缓存/锁/限流/短信/邮件/MQTT/MCP/ES等） |
| ruoyi-workflow | 6 | 工作流（任务/实例/定义/分类/SpEL/请假示例） |
| ruoyi-ai | 1 | AI OpenAPI |
| ruoyi-common-push | 1 | SSE 消息推送 |

---

## 三、核心 API 清单

### 3.1 认证模块 (ruoyi-admin)

| 路径 | Method | 用途 | 认证 |
|------|--------|------|------|
| `/auth/login` | POST | 多方式登录（密码/短信/邮箱/社交/小程序） | 无需 |
| `/auth/logout` | POST | 退出登录 | 需要 |
| `/auth/register` | POST | 用户注册 | 无需 |
| `/auth/code` | GET | 获取图片验证码 | 无需 |
| `/resource/sms/code` | GET | 发送短信验证码（限流 1次/60s） | 无需 |
| `/resource/email/code` | GET | 发送邮箱验证码 | 无需 |
| `/auth/social/callback` | POST | 社交登录回调绑定 | 无需 |
| `/` | GET | 首页引导 | 无需 |

### 3.2 用户管理 (002-user)

| 路径 | Method | 用途 |
|------|--------|------|
| `/system/user/list` | GET | 分页查询用户列表 |
| `/system/user/export` | POST | 导出用户列表 |
| `/system/user/importData` | POST | 导入用户数据 |
| `/system/user/importTemplate` | POST | 下载导入模板 |
| `/system/user/getInfo` | GET | 获取当前登录用户信息 |
| `/system/user/{userId}` | GET | 获取用户详情 |
| `/system/user` | POST | 新增用户 |
| `/system/user` | PUT | 修改用户 |
| `/system/user/{userIds}` | DELETE | 删除用户 |
| `/system/user/optionselect` | GET | 批量获取用户基础信息 |
| `/system/user/resetPwd` | PUT | 重置用户密码 |
| `/system/user/changeStatus` | PUT | 修改用户状态 |
| `/system/user/unlock/{userId}` | GET | 解锁用户 |
| `/system/user/authRole/{userId}` | GET | 获取用户授权角色 |
| `/system/user/authRole` | PUT | 用户授权角色 |
| `/system/user/deptTree` | GET | 获取部门树 |
| `/system/user/list/dept/{deptId}` | GET | 获取部门下用户列表 |

### 3.3 角色管理 (003-role)

| 路径 | Method | 用途 |
|------|--------|------|
| `/system/role/list` | GET | 分页查询角色列表 |
| `/system/role/export` | POST | 导出角色列表 |
| `/system/role/{roleId}` | GET | 获取角色详情 |
| `/system/role` | POST | 新增角色 |
| `/system/role` | PUT | 修改角色基础信息 |
| `/system/role/permission` | PUT | 修改角色权限 |
| `/system/role/changeStatus` | PUT | 修改角色状态 |
| `/system/role/{roleIds}` | DELETE | 删除角色 |
| `/system/role/optionselect` | GET | 角色选择列表 |
| `/system/role/authUser/allocatedList` | GET | 已分配用户列表 |
| `/system/role/authUser/unallocatedList` | GET | 未分配用户列表 |
| `/system/role/authUser/cancel` | PUT | 取消授权用户 |
| `/system/role/authUser/cancelAll` | PUT | 批量取消授权 |
| `/system/role/authUser/selectAll` | PUT | 批量授权用户 |
| `/system/role/deptTree/{roleId}` | GET | 角色部门树 |

### 3.4 菜单管理 (006-menu)

| 路径 | Method | 用途 |
|------|--------|------|
| `/system/menu/getRouters` | GET | 获取前端路由 |
| `/system/menu/list` | GET | 查询菜单列表 |
| `/system/menu/{menuId}` | GET | 获取菜单详情 |
| `/system/menu/treeselect` | GET | 菜单下拉树 |
| `/system/menu/roleMenuTreeselect/{roleId}` | GET | 角色菜单树 |
| `/system/menu` | POST | 新增菜单 |
| `/system/menu` | PUT | 修改菜单 |
| `/system/menu/{menuId}` | DELETE | 删除菜单 |
| `/system/menu/cascade/{menuIds}` | DELETE | 批量级联删除 |

### 3.5 部门管理 (004-dept)

| 路径 | Method | 用途 |
|------|--------|------|
| `/system/dept/list` | GET | 查询部门列表 |
| `/system/dept/list/exclude/{deptId}` | GET | 查询部门列表（排除节点） |
| `/system/dept/{deptId}` | GET | 获取部门详情 |
| `/system/dept` | POST | 新增部门 |
| `/system/dept` | PUT | 修改部门 |
| `/system/dept/{deptId}` | DELETE | 删除部门 |
| `/system/dept/optionselect` | GET | 部门选择列表 |

### 3.6 岗位管理 (005-post)

| 路径 | Method | 用途 |
|------|--------|------|
| `/system/post/list` | GET | 分页查询岗位列表 |
| `/system/post/export` | POST | 导出岗位列表 |
| `/system/post/{postId}` | GET | 获取岗位详情 |
| `/system/post` | POST | 新增岗位 |
| `/system/post` | PUT | 修改岗位 |
| `/system/post/{postIds}` | DELETE | 删除岗位 |
| `/system/post/optionselect` | GET | 岗位选择列表 |
| `/system/post/deptTree` | GET | 部门树 |

### 3.7 字典管理 (007-dict)

| 路径 | Method | 用途 |
|------|--------|------|
| `/system/dict/type/list` | GET | 字典类型列表 |
| `/system/dict/type/export` | POST | 导出字典类型 |
| `/system/dict/type/{dictId}` | GET | 字典类型详情 |
| `/system/dict/type` | POST | 新增字典类型 |
| `/system/dict/type` | PUT | 修改字典类型 |
| `/system/dict/type/{dictIds}` | DELETE | 删除字典类型 |
| `/system/dict/type/refreshCache` | DELETE | 刷新字典缓存 |
| `/system/dict/type/optionselect` | GET | 字典类型下拉列表 |
| `/system/dict/data/list` | GET | 字典数据列表 |
| `/system/dict/data/export` | POST | 导出字典数据 |
| `/system/dict/data/{dictCode}` | GET | 字典数据详情 |
| `/system/dict/data/type/{dictType}` | GET | 按类型查字典数据 |
| `/system/dict/data` | POST | 新增字典数据 |
| `/system/dict/data` | PUT | 修改字典数据 |
| `/system/dict/data/{dictCodes}` | DELETE | 删除字典数据 |

### 3.8 参数配置 (008-config)

| 路径 | Method | 用途 |
|------|--------|------|
| `/system/config/list` | GET | 参数列表 |
| `/system/config/export` | POST | 导出参数 |
| `/system/config/{configId}` | GET | 参数详情 |
| `/system/config/configKey/{configKey}` | GET | 按键名查参数值 |
| `/system/config` | POST | 新增参数 |
| `/system/config` | PUT | 修改参数 |
| `/system/config/updateByKey` | PUT | 按键名修改参数 |
| `/system/config/{configIds}` | DELETE | 删除参数 |
| `/system/config/refreshCache` | DELETE | 刷新参数缓存 |

### 3.9 通知公告 (009-notice)

| 路径 | Method | 用途 |
|------|--------|------|
| `/system/notice/list` | GET | 通知列表 |
| `/system/notice/{noticeId}` | GET | 通知详情 |
| `/system/notice` | POST | 新增通知（广播在线用户） |
| `/system/notice` | PUT | 修改通知 |
| `/system/notice/{noticeIds}` | DELETE | 删除通知 |

### 3.10 操作日志 (010-oper-log)

| 路径 | Method | 用途 |
|------|--------|------|
| `/monitor/operlog/list` | GET | 操作日志列表 |
| `/monitor/operlog/export` | POST | 导出操作日志 |
| `/monitor/operlog/{operIds}` | DELETE | 删除操作日志 |
| `/monitor/operlog/clean` | DELETE | 清空操作日志 |

### 3.11 登录日志 (011-login-log)

| 路径 | Method | 用途 |
|------|--------|------|
| `/monitor/loginInfo/list` | GET | 登录日志列表 |
| `/monitor/loginInfo/export` | POST | 导出登录日志 |
| `/monitor/loginInfo/{infoIds}` | DELETE | 删除登录日志 |
| `/monitor/loginInfo/clean` | DELETE | 清空登录日志 |
| `/monitor/loginInfo/unlock/{userName}` | GET | 解锁用户 |

### 3.12 在线用户 (012-online)

| 路径 | Method | 用途 |
|------|--------|------|
| `/monitor/online/list` | GET | 在线用户列表 |
| `/monitor/online/{tokenId}` | DELETE | 强制用户下线 |
| `/monitor/online` | GET | 当前用户在线设备 |
| `/monitor/online/myself/{tokenId}` | DELETE | 强退当前账号指定设备 |

### 3.13 缓存监控 (014-cache)

| 路径 | Method | 用途 |
|------|--------|------|
| `/monitor/cache` | GET | Redis 缓存监控信息 |

### 3.14 代码生成 (017-gen)

| 路径 | Method | 用途 |
|------|--------|------|
| `/tool/gen/list` | GET | 代码生成业务列表 |
| `/tool/gen/{tableId}` | GET | 修改代码生成业务 |
| `/tool/gen/db/list` | GET | 数据库表列表 |
| `/tool/gen/column/{tableId}` | GET | 数据表字段列表 |
| `/tool/gen/importTable` | POST | 导入表结构 |
| `/tool/gen` | PUT | 保存代码生成配置 |
| `/tool/gen/{tableIds}` | DELETE | 删除代码生成 |
| `/tool/gen/preview/{tableId}` | GET | 预览代码 |
| `/tool/gen/download/{tableId}` | GET | 下载代码（ZIP） |
| `/tool/gen/synchDb/{tableId}` | GET | 同步数据库 |
| `/tool/gen/batchGenCode` | GET | 批量生成代码 |
| `/tool/gen/getDataNames` | GET | 可用数据源列表 |

### 3.15 工作流 (feature-101)

| 路径 | Method | 用途 |
|------|--------|------|
| `/workflow/task/startWorkFlow` | POST | 启动流程 |
| `/workflow/task/completeTask` | POST | 办理任务 |
| `/workflow/task/pageByTaskWait` | GET | 待办任务列表 |
| `/workflow/task/pageByTaskFinish` | GET | 已办任务列表 |
| `/workflow/definition/list` | GET | 流程定义列表 |
| `/workflow/definition` | POST | 新增流程定义 |
| `/workflow/definition/publish/{id}` | PUT | 发布流程定义 |
| `/workflow/instance/pageByRunning` | GET | 运行中流程实例 |
| `/workflow/instance/getInfo/{businessId}` | GET | 流程实例详情 |
| `/workflow/category/list` | GET | 流程分类列表 |
| `/workflow/spel/list` | GET | 表达式列表 |

### 3.16 消息推送 (feature-118)

| 路径 | Method | 用途 |
|------|--------|------|
| `/resource/message` | GET | SSE 连接（默认消息推送通道） |
| `/resource/message/box` | GET | 用户消息盒子 |

### 3.17 AI 模块 (feature-114)

| 路径 | Method | 用途 |
|------|--------|------|
| `/snail-ai/user/register` | POST | 注册 AI 用户 |
| `/mcp` | - | MCP Server 端点（Spring AI） |

---

## 四、非 REST 协议端点

| 协议 | 路径 | 说明 |
|------|------|------|
| WebSocket | `/resource/message` | 实时消息推送（WebSocket 协议） |
| SSE | `/resource/message` | 服务端推送事件流 |
| MCP | `/mcp` | Model Context Protocol（Spring AI） |

---

## 五、全局错误码

| HTTP Code | 说明 |
|-----------|------|
| 200 | 成功 |
| 400 | 参数错误 |
| 401 | 未登录 / Token 失效 |
| 403 | 无权限 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |
| 601 | 演示模式禁止操作 |
