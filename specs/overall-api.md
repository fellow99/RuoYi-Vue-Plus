# 整体 API 规范 (overall-api.md)

**版本：** 5.5.3  
**最后更新：** 2026-03-13  
**项目：** RuoYi-Vue-Plus

---

## 一、API 设计规范

### 1.1 RESTful 规范

所有 API 遵循 RESTful 设计风格：

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | /api/resource | 获取资源列表（分页） |
| GET | /api/resource/{id} | 获取单个资源详情 |
| POST | /api/resource | 创建新资源 |
| PUT | /api/resource/{id} | 更新资源（全量） |
| PATCH | /api/resource/{id} | 更新资源（部分） |
| DELETE | /api/resource/{ids} | 删除资源（支持批量） |

### 1.2 统一响应格式

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {},
  "timestamp": 1710316800000
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| code | number | 状态码 |
| msg | string | 响应消息 |
| data | object | 响应数据 |
| timestamp | number | 时间戳 |

### 1.3 分页响应格式

```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "rows": [],
    "total": 100,
    "pageNum": 1,
    "pageSize": 10,
    "pages": 10
  },
  "timestamp": 1710316800000
}
```

### 1.4 错误响应格式

```json
{
  "code": 400,
  "msg": "参数错误：userName 不能为空",
  "data": null,
  "timestamp": 1710316800000
}
```

---

## 二、错误码规范

### 2.1 通用错误码

| 错误码 | 说明 | HTTP 状态码 |
|--------|------|----------|
| 200 | 成功 | 200 |
| 400 | 请求参数错误 | 400 |
| 401 | 未授权，需要登录 | 401 |
| 403 | 无权限访问 | 403 |
| 404 | 资源不存在 | 404 |
| 500 | 服务器内部错误 | 500 |

### 2.2 业务错误码

| 错误码 | 说明 |
|--------|------|
| 1001 | 用户不存在 |
| 1002 | 密码错误 |
| 1003 | 账号已停用 |
| 1004 | 账号已锁定 |
| 1005 | 验证码错误 |
| 1006 | 验证码过期 |
| 2001 | 角色不存在 |
| 2002 | 角色已被使用 |
| 3001 | 部门不存在 |
| 3002 | 部门下有用户 |
| 4001 | 岗位不存在 |
| 5001 | 字典类型不存在 |
| 6001 | 参数不存在 |
| 7001 | 租户不存在 |
| 7002 | 租户已过期 |
| 7003 | 租户账号数超限 |

---

## 三、认证相关 API

### 3.1 用户登录

```
POST /auth/login
```

**请求参数：**
```json
{
  "username": "admin",
  "password": "admin123",
  "code": "1234",
  "uuid": "xxx-xxx-xxx",
  "tenantId": "000000",
  "clientId": "default",
  "grantType": "password"
}
```

**响应：**
```json
{
  "code": 200,
  "msg": "登录成功",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "userInfo": {
      "userId": 1,
      "userName": "admin",
      "nickName": "管理员",
      "roles": ["admin"],
      "permissions": ["*:*:*"]
    }
  }
}
```

### 3.2 获取用户信息

```
GET /system/user/getInfo
```

**响应：**
```json
{
  "code": 200,
  "data": {
    "user": {...},
    "roles": [...],
    "permissions": [...]
  }
}
```

### 3.3 用户登出

```
POST /auth/logout
```

### 3.4 刷新 Token

```
POST /auth/refreshToken
```

### 3.5 获取验证码

```
GET /auth/captcha
```

**响应：**
```json
{
  "code": 200,
  "data": {
    "uuid": "xxx-xxx-xxx",
    "img": "data:image/png;base64,..."
  }
}
```

---

## 四、系统管理 API

### 4.1 用户管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/user/list | GET | 用户列表 |
| /system/user/{userId} | GET | 用户详情 |
| /system/user | POST | 新增用户 |
| /system/user | PUT | 修改用户 |
| /system/user/{userIds} | DELETE | 删除用户 |
| /system/user/changeStatus | PUT | 修改用户状态 |
| /system/user/resetPwd | PUT | 重置密码 |
| /system/user/importData | POST | 导入用户数据 |
| /system/user/export | POST | 导出用户数据 |
| /system/user/authRole/{userId} | GET | 用户角色分配 |
| /system/user/authRole | PUT | 保存用户角色 |
| /system/user/deptTree | GET | 部门树（用户分配用） |

### 4.2 角色管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/role/list | GET | 角色列表 |
| /system/role/{roleId} | GET | 角色详情 |
| /system/role | POST | 新增角色 |
| /system/role | PUT | 修改角色 |
| /system/role/{roleIds} | DELETE | 删除角色 |
| /system/role/changeStatus | PUT | 修改角色状态 |
| /system/role/authDataScope | PUT | 数据权限分配 |
| /system/role/authUser/selectAll | PUT | 批量选择用户 |
| /system/role/authUser/cancel | PUT | 取消用户授权 |

### 4.3 菜单管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/menu/list | GET | 菜单列表（树形） |
| /system/menu/{menuId} | GET | 菜单详情 |
| /system/menu | POST | 新增菜单 |
| /system/menu | PUT | 修改菜单 |
| /system/menu/{menuIds} | DELETE | 删除菜单 |
| /system/menu/treeselect | GET | 菜单树（选择器用） |
| /system/menu/roleMenuTreeselect | GET | 角色菜单树 |

### 4.4 部门管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/dept/list | GET | 部门列表（树形） |
| /system/dept/{deptId} | GET | 部门详情 |
| /system/dept | POST | 新增部门 |
| /system/dept | PUT | 修改部门 |
| /system/dept/{deptIds} | DELETE | 删除部门 |
| /system/dept/treeselect | GET | 部门树（选择器用） |

### 4.5 岗位管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/post/list | GET | 岗位列表 |
| /system/post/{postId} | GET | 岗位详情 |
| /system/post | POST | 新增岗位 |
| /system/post | PUT | 修改岗位 |
| /system/post/{postIds} | DELETE | 删除岗位 |

### 4.6 字典管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/dict/type/list | GET | 字典类型列表 |
| /system/dict/type/{dictId} | GET | 字典类型详情 |
| /system/dict/type | POST | 新增字典类型 |
| /system/dict/type | PUT | 修改字典类型 |
| /system/dict/type/{dictIds} | DELETE | 删除字典类型 |
| /system/dict/data/list | GET | 字典数据列表 |
| /system/dict/data/{dictCode} | GET | 字典数据详情 |
| /system/dict/data | POST | 新增字典数据 |
| /system/dict/data | PUT | 修改字典数据 |
| /system/dict/data/{dictCodes} | DELETE | 删除字典数据 |
| /system/dict/data/optionselect | GET | 字典数据下拉选项 |

### 4.7 参数管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/config/list | GET | 参数列表 |
| /system/config/{configId} | GET | 参数详情 |
| /system/config | POST | 新增参数 |
| /system/config | PUT | 修改参数 |
| /system/config/{configIds} | DELETE | 删除参数 |
| /system/config/configKey/{configKey} | GET | 根据 Key 查询参数 |

### 4.8 通知公告

| API | 方法 | 说明 |
|-----|------|------|
| /system/notice/list | GET | 公告列表 |
| /system/notice/{noticeId} | GET | 公告详情 |
| /system/notice | POST | 新增公告 |
| /system/notice | PUT | 修改公告 |
| /system/notice/{noticeIds} | DELETE | 删除公告 |

---

## 五、日志管理 API

### 5.1 操作日志

| API | 方法 | 说明 |
|-----|------|------|
| /monitor/operlog/list | GET | 操作日志列表 |
| /monitor/operlog/{operId} | GET | 操作日志详情 |
| /monitor/operlog/{operIds} | DELETE | 删除操作日志 |
| /monitor/operlog/clean | POST | 清空操作日志 |
| /monitor/operlog/export | POST | 导出操作日志 |

### 5.2 登录日志

| API | 方法 | 说明 |
|-----|------|------|
| /monitor/logininfor/list | GET | 登录日志列表 |
| /monitor/logininfor/{infoIds} | DELETE | 删除登录日志 |
| /monitor/logininfor/clean | POST | 清空登录日志 |
| /monitor/logininfor/unlock/{userName} | POST | 账户解锁 |
| /monitor/logininfor/export | POST | 导出登录日志 |

---

## 六、系统监控 API

### 6.1 在线用户

| API | 方法 | 说明 |
|-----|------|------|
| /monitor/online/list | GET | 在线用户列表 |
| /monitor/online/{tokenId} | DELETE | 强退在线用户 |
| /monitor/online/batch | DELETE | 批量强退 |

### 6.2 定时任务

| API | 方法 | 说明 |
|-----|------|------|
| /monitor/job/list | GET | 任务列表 |
| /monitor/job/{jobId} | GET | 任务详情 |
| /monitor/job | POST | 新增任务 |
| /monitor/job | PUT | 修改任务 |
| /monitor/job/{jobIds} | DELETE | 删除任务 |
| /monitor/job/changeStatus | PUT | 修改任务状态 |
| /monitor/job/run | PUT | 执行任务 |
| /monitor/job/download | GET | 下载任务配置 |

### 6.3 缓存监控

| API | 方法 | 说明 |
|-----|------|------|
| /monitor/cache | GET | 缓存信息 |
| /monitor/cache/getNames | GET | 缓存名称列表 |
| /monitor/cache/getKeys/{cacheName} | GET | 缓存键名列表 |
| /monitor/cache/getValue/{cacheName}/{cacheKey} | GET | 缓存内容 |
| /monitor/cache/clearCacheName/{cacheName} | DELETE | 清理缓存 |
| /monitor/cache/clearCacheKey/{cacheKey} | DELETE | 清理指定键缓存 |
| /monitor/cache/clearAll | DELETE | 清理所有缓存 |

### 6.4 服务器监控

| API | 方法 | 说明 |
|-----|------|------|
| /monitor/server | GET | 服务器信息 |

### 6.5 连接池监控

| API | 方法 | 说明 |
|-----|------|------|
| /monitor/druid | GET | Druid 监控数据 |

---

## 七、工具 API

### 7.1 代码生成

| API | 方法 | 说明 |
|-----|------|------|
| /tool/gen/list | GET | 生成表列表 |
| /tool/gen/importTable | POST | 导入表 |
| /tool/gen/preview/{tableId} | GET | 预览代码 |
| /tool/gen/download/{tableName} | GET | 下载代码 |
| /tool/gen/sync/{tableName} | PUT | 同步数据库 |
| /tool/gen/generateCode/{tableName} | POST | 生成代码到项目 |

### 7.2 系统接口

```
/swagger-ui/index.html
/v3/api-docs
```

---

## 八、租户管理 API (新增)

### 8.1 租户管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/tenant/list | GET | 租户列表 |
| /system/tenant/{tenantId} | GET | 租户详情 |
| /system/tenant | POST | 新增租户 |
| /system/tenant | PUT | 修改租户 |
| /system/tenant/{tenantIds} | DELETE | 删除租户 |
| /system/tenant/checkTenantNameUnique | POST | 检查租户名称唯一性 |

### 8.2 租户套餐管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/tenant/package/list | GET | 套餐列表 |
| /system/tenant/package/{packageId} | GET | 套餐详情 |
| /system/tenant/package | POST | 新增套餐 |
| /system/tenant/package | PUT | 修改套餐 |
| /system/tenant/package/{packageIds} | DELETE | 删除套餐 |

### 8.3 客户端管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/client/list | GET | 客户端列表 |
| /system/client/{clientId} | GET | 客户端详情 |
| /system/client | POST | 新增客户端 |
| /system/client | PUT | 修改客户端 |
| /system/client/{clientIds} | DELETE | 删除客户端 |

---

## 九、文件管理 API (新增)

### 9.1 文件管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/file/list | GET | 文件列表 |
| /system/file/{fileId} | GET | 文件详情 |
| /system/file | POST | 上传文件 |
| /system/file/{fileIds} | DELETE | 删除文件 |
| /system/file/download/{fileId} | GET | 下载文件 |

### 9.2 文件配置管理

| API | 方法 | 说明 |
|-----|------|------|
| /system/oss/config/list | GET | 配置列表 |
| /system/oss/config/{configId} | GET | 配置详情 |
| /system/oss/config | POST | 新增配置 |
| /system/oss/config | PUT | 修改配置 |
| /system/oss/config/{configIds} | DELETE | 删除配置 |
| /system/oss/config/checkConfigKeyUnique | POST | 检查配置 Key 唯一性 |

---

## 十、请求头规范

### 10.1 认证头

```
Authorization: Bearer {token}
```

### 10.2 租户头

```
tenant-id: {tenantId}
```

### 10.3 其他头

```
Content-Type: application/json
User-Agent: {client-type}
```

---

## 十一、接口限流

### 11.1 限流注解

```java
@RateLimiter(time = 60, count = 10, limitType = LimitType.IP)
```

### 11.2 限流类型

| 类型 | 说明 |
|------|------|
| GLOBAL | 全局限流 |
| IP | 按 IP 限流 |
| USER | 按用户限流 |
| API | 按接口限流 |

### 11.3 限流参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| time | 时间窗口（秒） | 60 |
| count | 请求次数 | 10 |
| limitType | 限流类型 | IP |

---

## 十二、认证授权机制

### 12.1 Sa-Token 集成

RuoYi-Vue-Plus 使用 Sa-Token 作为权限认证框架：

| 功能 | 说明 |
|------|------|
| 登录认证 | 支持账号密码、短信、社交登录 |
| 权限验证 | 支持角色、权限标识验证 |
| 会话管理 | 支持 Token、Session 管理 |
| 单点登录 | 支持 SSO 单点登录 |
| OAuth2 | 支持 OAuth2 授权 |

### 12.2 JWT Token 结构

```
Header.Payload.Signature

Header: {"alg":"HS256","typ":"JWT"}
Payload: {
  "loginId": "1",
  "loginType": "login",
  "tokenTimeout": 7200,
  "isLogin": true
}
```

### 12.3 权限注解

```java
// 登录校验
@SaCheckLogin

// 角色校验
@SaCheckRole("admin")

// 权限校验
@SaCheckPermission("system:user:add")

// 二级认证
@SaCheckSafe()

// HTTP 基本校验
@SaCheckBasic(account="admin", password="123456")
```

---

**文档结束**
