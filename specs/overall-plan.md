# 整体技术方案 (overall-plan.md)

**版本：** 6.0.0  
**最后更新：** 2026-08-10  
**项目：** RuoYi-Vue-Plus

---

## 一、技术上下文

### 1.1 运行时环境
- **JDK：** 21（推荐）/ 25（兼容）
- **构建工具：** Maven 3.9+
- **Web 容器：** Jetty 12（基于 Netty）
- **数据库：** MySQL 8.0+（主），Oracle/PostgreSQL/SQLServer（可选）
- **缓存：** Redis 7.0+
- **对象存储：** MinIO / RustFS / S3 兼容

### 1.2 关键依赖

| 分类 | 依赖 | 版本 | 作用 |
|------|------|------|------|
| 框架 | Spring Boot | 4.1.0 | 应用框架 |
| 认证 | Sa-Token | 1.45.0 | 权限认证 |
| ORM | MyBatis-Plus | 3.5.17 | 数据访问 |
| 缓存 | Redisson | 4.6.1 | Redis 客户端 |
| 任务 | SnailJob | 2.0.2 | 分布式调度 |
| 工作流 | Warm-Flow | 1.8.9 | 流程引擎 |
| AI | Spring AI | 2.0.0 | AI 框架 |
| 规则 | LiteFlow | 2.16.0 | 规则引擎 |

---

## 二、宪法合规检查

| 原则 | 状态 | 说明 |
|------|------|------|
| 插件化设计 | ✅ | 25 个 common 子模块，按需引入 |
| 分布式就绪 | ✅ | JWT 无状态 + Redisson + SnailJob |
| 扩展性优先 | ✅ | 接口化设计 + 注解驱动 |
| 前后端分离 | ✅ | 独立前端项目 plus-ui |
| 安全优先 | ✅ | 传输加密 + 字段加密 + 脱敏 + XSS/SQL 防护 |
| Alibaba 规范 | ✅ | .editorconfig 统一格式化 |
| JavaDoc 文档 | ✅ | SpringDoc + Therapi 运行时读取 |
| 单元测试 | ⚠️ | 基础测试覆盖，需持续完善 |

---

## 三、实现策略概述

### 3.1 多模块工程结构

```
ruoyi-admin          → Spring Boot 启动入口，组装所有模块
ruoyi-api            → 跨模块 RPC 接口定义层（解耦模块间调用）
ruoyi-common (×25)   → 基础设施层（AutoConfiguration，按需激活）
ruoyi-modules (×6)   → 业务层（system/gen/job/demo/workflow/ai）
ruoyi-extend (×3)    → 独立部署服务（monitor/snailjob/snailai）
```

### 3.2 认证流程

```
用户请求 → SaTokenInterceptor（JWT 解析 → 登录校验 → 权限校验）
         → Controller 方法 → @SaCheckRole / @SaCheckPermission 注解校验
         → Service 层 → 数据权限过滤（MyBatis-Plus 拦截器自动注入）
```

### 3.3 消息推送架构

```
业务触发（通知/工作流/消息）→ PushHelper
    ├── SSE（默认）→ SseEmitterSessionManager → 浏览器
    └── WebSocket → WebSocketSessionManager → 浏览器
```

---

## 四、跨领域关注点

### 4.1 错误处理
- 全局异常拦截：`GlobalExceptionHandler`（`@RestControllerAdvice`）
- 业务异常：`ServiceException`，`UserException` 系列
- 文件异常：`FileException` 系列
- 框架异常：各模块专用 ExceptionHandler

### 4.2 日志记录
- 操作日志：`@Log` 注解 + `LogAspect` → `OperLogEvent` → 异步持久化
- 登录日志：`AuthController` → `LoginInfoEvent` → 异步持久化
- SQL 日志：`SqlLogInterceptor`（开发环境输出完整 SQL）
- 应用日志：Logback（`logback-plus.xml`）

### 4.3 数据权限
- 注解驱动：`@DataPermission` 标记需要数据权限的 Mapper
- 插件注入：`PlusDataPermissionInterceptor` 自动拼接 SQL 条件
- 范围类型：全部/自定义/本部门/本部门及以下/仅本人

### 4.4 缓存策略
- Spring Cache 注解：`@Cacheable`, `@CachePut`, `@CacheEvict`
- 扩展功能：过期时间、最大空闲时间、组最大长度
- 字典缓存：`CacheNames.SYS_DICT`，`/system/dict/type/refreshCache` 刷新
- 参数缓存：`CacheNames.SYS_CONFIG`，`/system/config/refreshCache` 刷新

---

## 五、测试策略

### 5.1 测试类型
- **单元测试：** Maven Surefire，核心 Service 方法
- **集成测试：** Spring Boot Test，数据库操作验证
- **API 测试：** SpringDoc + Swagger UI 手动验证

### 5.2 测试覆盖
- Maven 默认跳过测试（`maven.test.skip=true`）
- 开发环境可启用测试（`mvn test -DskipTests=false`）
- 核心模块：ruoyi-system（用户/角色/菜单 CRUD）

---

## 六、部署策略

### 6.1 构建
```bash
# 多环境打包
mvn clean package -P dev     # 开发环境
mvn clean package -P prod    # 生产环境
```

### 6.2 Docker 部署
```bash
cd script/docker
docker-compose up -d          # 一键启动全部服务
```

### 6.3 传统部署
```bash
# Linux
./script/bin/ry.sh start
# Windows
script\bin\ry.bat
```
