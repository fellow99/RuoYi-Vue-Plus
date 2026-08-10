# 011-login-log 技术方案 (plan.md)

> 对应规格：spec.md | 模块：011-login-log | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17
- Sa-Token（登录认证框架）、Spring Event（事件总线）
- Hutool（`UserAgentUtil` User-Agent 解析）

### 1.2 依赖
- ruoyi-admin（`SysLoginService.recordLoginInfo()` 发布事件、`SysRegisterService` 发布注册事件）
- ruoyi-common-log（`LoginInfoEvent`）
- ruoyi-common-core（`R`、`PageResult`、`AddressUtils`、`ServletUtils`、`Constants` 状态常量）
- ruoyi-common-mybatis（`BaseMapperPlus`、`PageQuery`、`LambdaCrudChainWrapper`）
- ruoyi-common-excel（`ExcelBuilder`、Apache Fesod）
- ruoyi-common-security（Sa-Token `@SaCheckPermission`）
- ruoyi-common-redis（`@Lock4j` 分布式锁、`RedisUtils`、`@RepeatSubmit` 防重复提交）
- ruoyi-system（`ISysClientService.queryByClientId()` 查询客户端信息）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → Service(接口+实现) → Mapper → Entity |
| 注解驱动 | ✅ | `@SaCheckPermission` 权限控制，`@Log` 操作日志记录 |
| 异步处理 | ✅ | `@Async` + `@EventListener` 实现登录日志异步持久化 |
| 事件驱动 | ✅ | `LoginInfoEvent` → Spring Event → `SysLoginInfoServiceImpl.recordLoginInfo()` |

## 3. 数据模型

### 3.1 核心表
- `sys_login_info` — 登录日志表（无逻辑删除，物理删除）

### 3.2 关键字段规则
- `info_id`: 雪花ID主键（`@TableId`）
- `status`: '0'=成功，'1'=失败（String 类型，来自 `Constants.SUCCESS` / `Constants.FAIL`）
- `ipaddr`: 登录 IP（来自 `ServletUtils.getClientIP()`）
- `loginLocation`: IP 归属地（来自 `AddressUtils.getRealAddressByIP()`）
- `browser`: 浏览器名称（Hutool `UserAgentUtil.parse()` 解析）
- `os`: 操作系统名称（Hutool `UserAgentUtil.parse()` 解析）
- `msg`: 登录提示消息（如"登录成功"、"密码错误"、"账户已锁定"等）
- `loginTime`: 登录时间（`LocalDateTime.now()`）
- `clientKey` / `deviceType`: 若传入 `clientId`，则通过 `ISysClientService.queryByClientId()` 查询客户端信息填充

### 3.3 Redis 缓存模型
- `pwd_err_cnt:{userName}`: 密码错误计数（Integer），过期时间 = `lockTime` 分钟（默认 10 分钟）
- 计数器由 `SysLoginService.checkLogin()` 管理，解锁时由 Controller 删除

## 4. 接口契约

### 4.1 对外 REST API（SysLoginInfoController，`/monitor/loginInfo`）

| 方法 | 路径 | 权限 | 说明 |
|------|------|------|------|
| GET | `/list` | `monitor:logininfo:list` | 分页查询登录日志 |
| POST | `/export` | `monitor:logininfo:export` | 导出登录日志 Excel |
| DELETE | `/{infoIds}` | `monitor:logininfo:remove` | 批量删除登录日志 |
| DELETE | `/clean` | `monitor:logininfo:remove` | 清空登录日志（分布式锁） |
| GET | `/unlock/{userName}` | `monitor:logininfo:unlock` | 解锁账户（防重复提交） |

### 4.2 事件契约
- **发布方**:
  - `SysLoginService.recordLoginInfo(username, status, message)` → `LoginInfoEvent` → `publishEvent()`
  - `SysLoginService.checkLogin()` → 登录失败计数递增及锁定判定
  - `SysRegisterService` → 注册成功事件
- **消费方**: `SysLoginInfoServiceImpl.recordLoginInfo(LoginInfoEvent)` — `@Async` + `@EventListener`
- **事件字段**: username, status, message, ip, userAgent, clientId, args

### 4.3 消费接口
- `ISysClientService.queryByClientId(clientId)` — 查询客户端配置信息（clientKey, deviceType）

## 5. 实现策略

### 5.1 架构模式
**事件驱动 + 异步持久化**

```
AuthController(登录/登出) → SysLoginService.recordLoginInfo() → publishEvent(LoginInfoEvent)
  → SysLoginInfoServiceImpl.recordLoginInfo(@Async @EventListener)
    → UserAgentUtil 解析浏览器/OS → AddressUtils 解析IP归属地
    → ISysClientService 查询客户端 → insertLoginInfo()
```

### 5.2 关键流程

**登录日志记录流程**:
1. `SysLoginService.recordLoginInfo(username, status, message)` 构建 `LoginInfoEvent`
2. 从 `HttpServletRequest` 中提取 `ip`（`ServletUtils.getClientIP()`）、`User-Agent`、`clientId`（请求头 `CLIENT_KEY`）
3. 通过 `SpringUtils.context().publishEvent()` 发布事件
4. `SysLoginInfoServiceImpl.recordLoginInfo()` 异步消费事件：
   - `UserAgentUtil.parse(userAgent)` 解析出浏览器和 OS
   - 若传入 `clientId`，调用 `clientService.queryByClientId()` 获取 `clientKey` 和 `deviceType`
   - `AddressUtils.getRealAddressByIP(ip)` 获取 IP 归属地
   - 状态映射：`LOGIN_SUCCESS`/`LOGOUT`/`REGISTER` → `Constants.SUCCESS`，`LOGIN_FAIL` → `Constants.FAIL`
   - 通过 `Slf4j` 日志输出格式化登录信息（含 IP、地址、用户名、状态、消息）
   - `MapstructUtils.convert(loginInfo, SysLoginInfo.class)` + `loginTime` → `loginInfoMapper.insert()`

**账户解锁流程**:
1. `GET /monitor/loginInfo/unlock/{userName}` → `@RepeatSubmit()` 防重复提交
2. 拼接 Redis key: `CacheNames.PWD_ERR_CNT_KEY + userName` = `pwd_err_cnt:{userName}`
3. 若 key 存在 → `RedisUtils.deleteObject(loginName)` 删除错误计数
4. 返回成功（key 不存在也不报错）

**密码错误计数与锁定流程**（由 `SysLoginService.checkLogin()` 管理）:
1. 从 Redis 获取 `pwd_err_cnt:{username}` 当前错误计数
2. 若 ≥ `maxRetryCount`（默认 5）→ 发布失败事件 + 抛出 `UserException("密码错误次数超过限制，请{N}分钟后再试")`
3. 执行认证 `supplier`，若失败 → 计数 +1，`RedisUtils.setCacheObject(key, count, Duration.ofMinutes(lockTime))`
4. 认证成功 → `RedisUtils.deleteObject(key)` 清空计数

### 5.3 错误处理
- Controller 层异常由全局异常处理器统一转为 `R` 响应
- 解锁操作中若 Redis key 不存在，不报错，直接返回成功

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysLoginInfoController | REST API | system/controller/monitor/ |
| ISysLoginInfoService | 业务接口 | system/service/ |
| SysLoginInfoServiceImpl | 业务实现 + 事件监听 | system/service/impl/ |
| SysLoginInfoMapper | 数据访问 | system/mapper/ |
| SysLoginInfo.java | 实体（sys_login_info） | system/domain/ |
| SysLoginInfoBo.java | 请求体 | system/domain/bo/ |
| SysLoginInfoVo.java | 响应体 / Excel 导出 VO | system/domain/vo/ |
| SysLoginService.java | 登录服务（发布事件、密码锁定逻辑） | ruoyi-admin/web/service/ |
| SysRegisterService.java | 注册服务（发布注册事件） | ruoyi-admin/web/service/ |
| LoginInfoEvent.java | 登录事件定义 | common-log/event/ |
| CacheNames.java | 缓存常量（PWD_ERR_CNT_KEY） | common-core/constant/ |
