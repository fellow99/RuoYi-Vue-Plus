# 008-config 技术方案 (plan.md)

> 对应规格：spec.md | 模块：008-config | 最后更新：2026-08-10

## 1. 技术上下文

### 1.1 运行时环境
- Java 21, Spring Boot 4.1, MyBatis-Plus 3.5.17

### 1.2 依赖
- ruoyi-common-core（R 响应、CacheNames）
- ruoyi-common-mybatis（BaseMapperPlus、分页）
- ruoyi-common-redis（CacheUtils、Spring Cache）
- ruoyi-common-security（Sa-Token 权限）
- ruoyi-common-excel（ExcelBuilder 导出）
- ruoyi-common-log（@Log 操作日志）

## 2. 宪法合规

| 原则 | 状态 | 说明 |
|------|------|------|
| 分层规范 | ✅ | Controller → Service(接口+实现) → Mapper → Entity |
| 注解驱动 | ✅ | @SaCheckPermission 权限控制，@Cacheable/@CacheEvict 缓存 |
| 缓存策略 | ✅ | selectConfigByKey() 走 CacheUtils 缓存，CRUD 后驱逐 |

## 3. 数据模型

### 3.1 核心表
- `sys_config` — 参数配置表（configId 主键，configKey 业务唯一键）

### 3.2 关键字段规则
- `configName`: 参数名称（中文说明，如"用户管理-账号初始密码"）
- `configKey`: 参数键名，全局唯一，如 "sys.account.registerUser"
- `configValue`: 参数键值，可存储布尔值、数字、字符串
- `configType`: Y=系统内置参数 / N=用户自定义参数，内置参数不建议删除
- 继承 BaseEntity：createBy, createTime, updateBy, updateTime, remark

## 4. 接口契约

### 4.1 提供接口

**SysConfigController** (`/system/config`):

| 方法 | 路径 | 权限 | 说明 |
|------|------|------|------|
| GET | `/list` | system:config:list | 分页查询参数配置列表 |
| POST | `/export` | system:config:export | 导出 Excel |
| GET | `/{configId}` | system:config:query | 查询参数配置详情 |
| GET | `/configKey/{configKey}` | — | 按键名查询参数值（公开） |
| POST | `/` | system:config:add | 新增参数配置 |
| PUT | `/` | system:config:edit | 修改参数配置 |
| PUT | `/updateByKey` | system:config:edit | 按 configKey 修改参数值 |
| DELETE | `/{configIds}` | system:config:remove | 批量删除参数配置 |
| DELETE | `/refreshCache` | system:config:remove | 刷新参数缓存 |

### 4.2 消费接口
- 无外部依赖，参数配置为基础模块

### 4.3 业务调用模式
- 业务代码通过 `configService.selectConfigByKey("sys.account.registerUser")` 获取参数值
- `selectRegisterEnabled()` 封装了注册开关的判断逻辑（读取 configKey 转为 boolean）

## 5. 实现策略

### 5.1 架构模式
标准四层 CRUD：

```
SysConfigController(/system/config) → ISysConfigService/SysConfigServiceImpl → SysConfigMapper → MySQL
```

### 5.2 关键算法
- **缓存策略**：CacheUtils 手动管理
  - 读取：`selectConfigByKey()` → `CacheUtils.get(CacheNames.SYS_CONFIG, configKey)` → 缓存未命中则查 DB 并写入缓存
  - 写入：`insertConfig()` / `updateConfig()` → 写 DB → `CacheUtils.put()` 更新缓存
  - 删除：`deleteConfigByIds()` → 删 DB → `CacheUtils.remove()` 逐出缓存
  - 刷新：`resetConfigCache()` → `CacheUtils.clear(CacheNames.SYS_CONFIG)` 清空所有
- **configKey 唯一性校验**：新增/修改时通过 Lambda 查询是否存在同 key 记录
- **注册开关判断**：`selectRegisterEnabled()` 读取 `sys.account.registerUser` 的 configValue，转为 boolean

### 5.3 错误处理
- configKey 重复 → R.fail("参数键名已存在")
- 全局异常转 R 响应

## 6. 文件清单

| 文件 | 用途 | 路径 |
|------|------|------|
| SysConfigController | 参数配置 REST API | system/controller/system/ |
| ISysConfigService | 参数配置业务接口 | system/service/ |
| SysConfigServiceImpl | 参数配置业务实现 | system/service/impl/ |
| SysConfigMapper | 参数配置数据访问 | system/mapper/ |
| SysConfig.java | 参数配置实体 | system/domain/ |
| SysConfigBo.java | 参数配置请求体 | system/domain/bo/ |
| SysConfigVo.java | 参数配置响应体 | system/domain/vo/ |
