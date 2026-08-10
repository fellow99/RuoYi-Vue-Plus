# 012-online 技术方案 (plan.md)

> 对应规格：spec.md | 模块：012-online | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, Sa-Token 1.44+
- Redis（Redisson 客户端），虚拟线程（Virtual Threads）
- Jetty Web 容器

### 1.2 依赖
- ruoyi-common-core（`R`、`PageResult`、`CacheNames`、`ThreadUtils`、`StreamUtils`、`StringUtils`）
- ruoyi-common-redis（`RedisUtils`、`@RepeatSubmit` 防重复提交）
- ruoyi-common-security（Sa-Token `@SaCheckPermission`）
- ruoyi-common-log（`@Log` 注解，`BusinessType.FORCE`）
- ruoyi-common-satoken（Sa-Token `StpUtil`）
- ruoyi-api（`UserOnlineDTO` DTO 定义）
- Hutool（`BeanUtil.copyToList()` 对象转换）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller 直接操作 Redis + Sa-Token，无 Service/Mapper 层（纯缓存视图） |
| 注解驱动 | ✅ | `@SaCheckPermission` 权限控制，`@Log` 操作日志，`@RepeatSubmit` 防重复 |
| 缓存驱动 | ✅ | 数据完全基于 Redis，`SysUserOnline` 非数据库实体 |
| 虚拟线程 | ✅ | `ThreadUtils.virtualSubmitAll()` 并行加载 token 信息 |

## 3. 数据模型

### 3.1 存储模型（纯 Redis，无数据库表）

| Redis Key | 类型 | 内容 | 生命周期 |
|-----------|------|------|----------|
| `online_tokens:{token}` | String(JSON) | `UserOnlineDTO` 序列化 | 登录时写入，登出/过期时删除 |
| `Authorization:login:token:{token}` | String | Sa-Token 登录凭证 | Sa-Token 管理 |

### 3.2 关键字段规则（SysUserOnline / UserOnlineDTO）
- `tokenId`: token 值（Sa-Token tokenValue），从 Redis key 中提取（`StringUtils.substringAfterLast(key, ":")`）
- `userName`: 登录用户名
- `deptName`: 部门名称
- `clientKey`: 客户端标识（如 `pc`、`app`、`wechat`）
- `deviceType`: 设备类型（如 `pc`、`mobile`）
- `ipaddr`: 登录 IP
- `loginLocation`: IP 归属地
- `browser`: 浏览器名称
- `os`: 操作系统名称
- `loginTime`: 登录时间戳（Long 类型，毫秒）

### 3.3 SysUserOnline 实体
- 非 `@TableName` 注解，非数据库实体
- 仅作为 VO（视图对象），由 `UserOnlineDTO` 通过 `BeanUtil.copyToList()` 转换而来
- 位于 `ruoyi-modules/ruoyi-system` 的 `domain` 包中

## 4. 接口契约

### 4.1 对外 REST API（SysUserOnlineController，`/monitor/online`）

| 方法 | 路径 | 权限 | 说明 |
|------|------|------|------|
| GET | `/list?ipaddr=&userName=` | `monitor:online:list` | 管理员查看在线用户列表 |
| DELETE | `/{tokenId}` | `monitor:online:forceLogout` | 管理员强制踢出用户 |
| GET | `/` | 无（已登录即可） | 当前用户查看自己的在线设备 |
| DELETE | `/myself/{tokenId}` | 无（已登录即可） | 当前用户踢出自己的设备 |

### 4.2 消费接口
- `StpUtil.stpLogic.getTokenActiveTimeoutByToken(token)` — 检测 token 是否过期
- `StpUtil.kickoutByTokenValue(tokenId)` — 踢出 token
- `StpUtil.getTokenValueListByLoginId(loginId)` — 获取指定账号的所有 token
- `StpUtil.getLoginIdAsString()` — 获取当前登录用户 ID
- `RedisUtils.keys(pattern)` — 扫描 Redis key
- `RedisUtils.getCacheObject(key)` — 读取缓存对象

## 5. 实现策略

### 5.1 架构模式
**纯 Redis 缓存视图 + Sa-Token 踢出机制**

```
Controller → RedisUtils.keys("online_tokens:*") → 并行加载 UserOnlineDTO → 过滤/排序
  → BeanUtil.copyToList → PageResult<SysUserOnline>
```

**无 Service 层、无 Mapper 层** — 整个模块只有 Controller + VO 实体，所有数据直接从 Redis 读取。

### 5.2 关键流程

**管理员查看在线用户列表**:
1. `RedisUtils.keys(CacheNames.ONLINE_TOKEN_KEY + "*")` 扫描所有 `online_tokens:*` key
2. 对每个 key，提取 token 值：`StringUtils.substringAfterLast(key, ":")`
3. 用 `StpUtil.stpLogic.getTokenActiveTimeoutByToken(token)` 检测是否过期（< -1 表示已过期），过期则跳过
4. 用 `RedisUtils.getCacheObject(CacheNames.ONLINE_TOKEN_KEY + token)` 读取 `UserOnlineDTO`
5. 使用 `ThreadUtils.virtualSubmitAll(suppliers)` 并行加载所有 token 信息（虚拟线程）
6. 移除 null 值（过期 token），按 ipaddr / userName 过滤（支持 AND 组合）
7. `Collections.reverse()` 倒序排列（最近登录在前）
8. `BeanUtil.copyToList(userOnlineDTOList, SysUserOnline.class)` 转换为 VO
9. `PageResult.build(userOnlineList)` 返回（不分页，返回全量在线用户）

**管理员强制踢出用户**:
1. `DELETE /monitor/online/{tokenId}` → `@RepeatSubmit()` 防重复
2. `StpUtil.kickoutByTokenValue(tokenId)` 调用 Sa-Token 踢出
3. 捕获 `NotLoginException`（token 可能已过期，忽略异常）
4. 返回成功

**当前用户查看自己的在线设备**:
1. `StpUtil.getTokenValueListByLoginId(StpUtil.getLoginIdAsString())` 获取当前用户所有 token
2. 并行加载每个 token 的 `UserOnlineDTO`（虚拟线程）
3. 过滤已过期 token，按登录时间倒序排列
4. 返回设备列表

**当前用户踢出自己的设备**:
1. `DELETE /monitor/online/myself/{tokenId}` → `@RepeatSubmit()` 防重复
2. `StpUtil.getTokenValueListByLoginId(StpUtil.getLoginIdAsString())` 获取当前用户所有 token
3. 校验 tokenId 是否在当前用户的 token 列表中（`filter(key -> key.equals(tokenId))`），防止误踢其他用户
4. 找到匹配 token → `StpUtil.kickoutByTokenValue(tokenId)` 踢出
5. 捕获 `NotLoginException` 忽略

### 5.3 错误处理
- 踢出操作中若 token 已过期或不存在，捕获 `NotLoginException` 并忽略，不报错
- 当前用户踢出设备时，只处理匹配的 token，不匹配的 token 不处理

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysUserOnlineController | REST API（4个接口） | system/controller/monitor/ |
| SysUserOnline.java | 在线用户 VO（非数据库实体） | system/domain/ |
| UserOnlineDTO.java | Redis 缓存的在线用户 DTO | ruoyi-api/system/api/domain/ |
| CacheNames.java | 缓存常量（ONLINE_TOKEN_KEY） | common-core/constant/ |
