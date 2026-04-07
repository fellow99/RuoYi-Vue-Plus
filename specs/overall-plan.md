# 整体技术方案 (overall-plan.md)

**版本：** 5.5.3  
**最后更新：** 2026-03-13  
**项目：** RuoYi-Vue-Plus

---

## 一、技术架构

### 1.1 系统架构图

```
┌─────────────────────────────────────────────────────────────────┐
│                        前端层 (Vue3 + TS)                        │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │
│  │ Element  │ │  Pinia   │ │  Router  │ │  Axios   │           │
│  │  Plus    │ │  状态管理 │ │  路由    │ │  HTTP    │           │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘           │
└─────────────────────────────────────────────────────────────────┘
                              ↓ HTTPS/JSON
┌─────────────────────────────────────────────────────────────────┐
│                        网关层 (Nginx)                            │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  负载均衡 | SSL 终止 | 静态资源 | 请求转发                   │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                      应用层 (Spring Boot)                        │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Controller 层                          │   │
│  │  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐            │   │
│  │  │ 认证   │ │ 系统   │ │ 租户   │ │ 监控   │            │   │
│  │  │ 控制器 │ │ 控制器 │ │ 控制器 │ │ 控制器 │            │   │
│  │  └────────┘ └────────┘ └────────┘ └────────┘            │   │
│  └──────────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Service 层                             │   │
│  │  ┌────────────────────────────────────────────────────┐  │   │
│  │  │  业务逻辑 | 事务管理 | 权限校验 | 数据校验            │  │   │
│  │  └────────────────────────────────────────────────────┘  │   │
│  └──────────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Mapper 层                              │   │
│  │  ┌────────────────────────────────────────────────────┐  │   │
│  │  │  MyBatis-Plus | 动态 SQL | 数据映射                  │  │   │
│  │  └────────────────────────────────────────────────────┘  │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                        数据层                                    │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │
│  │  MySQL   │ │  Redis   │ │  MinIO   │ │ 其他 DB  │           │
│  │  主数据库 │ │  缓存    │ │  文件存储 │ │  异构    │           │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘           │
└─────────────────────────────────────────────────────────────────┘
```

### 1.2 技术栈详情

| 层级 | 技术 | 版本 | 用途 |
|------|------|------|------|
| **前端** | Vue | 3.x | 前端框架 |
| | TypeScript | 5.x | 类型系统 |
| | Element Plus | 2.x | UI 组件库 |
| | Pinia | 2.x | 状态管理 |
| | Vue Router | 4.x | 路由管理 |
| | Axios | 1.x | HTTP 客户端 |
| **后端** | JDK | 17/21 | 运行环境 |
| | Spring Boot | 3.5.x | 应用框架 |
| | Sa-Token | 1.44.0 | 权限认证 |
| | MyBatis-Plus | 3.5.16 | ORM 框架 |
| | Redisson | 3.52.0 | Redis 客户端 |
| | HikariCP | - | 数据库连接池 |
| | Undertow | - | Web 容器 |
| **中间件** | MySQL | 8.0+ | 关系数据库 |
| | Redis | 5-7 | 缓存数据库 |
| | MinIO | Latest | 对象存储 |
| | SnailJob | 1.9.0 | 分布式任务调度 |
| **工具** | Hutool | 5.x | 工具类库 |
| | Lombok | 1.18.x | 代码简化 |
| | SpringDoc | 2.x | API 文档 |
| | Jackson | 2.x | JSON 序列化 |

---

## 二、模块设计

### 2.1 模块划分原则

1. **单一职责** - 每个模块只负责一个功能领域
2. **低耦合** - 模块间通过接口通信，减少直接依赖
3. **高内聚** - 相关功能组织在同一模块内
4. **可插拔** - 模块可独立启用/禁用

### 2.2 核心模块说明

#### ruoyi-admin (主启动模块)
- 应用入口
- Servlet 初始化
- 全局配置

#### ruoyi-common-* (通用模块群)
| 模块 | 用途 |
|------|------|
| ruoyi-common-core | 核心工具类、常量、异常 |
| ruoyi-common-security | 安全相关工具 |
| ruoyi-common-satoken | Sa-Token 集成 |
| ruoyi-common-mybatis | MyBatis-Plus 配置 |
| ruoyi-common-redis | Redis/Redisson 配置 |
| ruoyi-common-web | Web 配置、拦截器 |
| ruoyi-common-json | JSON 序列化配置 |
| ruoyi-common-doc | Swagger/SpringDoc 配置 |
| ruoyi-common-log | 日志配置 |
| ruoyi-common-tenant | 多租户支持 |
| ruoyi-common-encrypt | 数据加解密 |
| ruoyi-common-sensitive | 数据脱敏 |
| ruoyi-common-translation | 数据翻译 |
| ruoyi-common-idempotent | 幂等性支持 |
| ruoyi-common-ratelimiter | 限流支持 |
| ruoyi-common-excel | Excel 处理 |
| ruoyi-common-oss | 对象存储 |
| ruoyi-common-sms | 短信服务 |
| ruoyi-common-mail | 邮件服务 |
| ruoyi-common-job | 任务调度 |
| ruoyi-common-websocket | WebSocket 支持 |
| ruoyi-common-sse | SSE 推送 |

#### ruoyi-modules-* (业务模块群)
| 模块 | 用途 |
|------|------|
| ruoyi-system | 系统管理功能 |
| ruoyi-generator | 代码生成 |
| ruoyi-job | 定时任务 |
| ruoyi-demo | Demo 案例 |
| ruoyi-workflow | 工作流引擎 |

#### ruoyi-extend-* (扩展模块群)
| 模块 | 用途 |
|------|------|
| ruoyi-monitor-admin | SpringBoot-Admin 监控 |
| ruoyi-snailjob-server | SnailJob 服务端 |

---

## 三、分层设计

### 3.1 Controller 层

**职责：**
- 接收 HTTP 请求
- 参数校验
- 调用 Service 层
- 返回统一响应格式

**规范：**
```java
@RestController
@RequestMapping("/system/user")
@Validated
@RequiredArgsConstructor
public class SysUserController {
    private final ISysUserService userService;
    
    @SaCheckPermission("system:user:list")
    @GetMapping("/list")
    public TableResponse<SysUserVo> list(SysUserBo bo, PageQuery pageQuery) {
        return userService.queryPageList(bo, pageQuery);
    }
}
```

### 3.2 Service 层

**职责：**
- 业务逻辑处理
- 事务管理
- 权限校验
- 数据校验

**规范：**
```java
@Service
@RequiredArgsConstructor
public class SysUserServiceImpl implements ISysUserService {
    private final ISysRoleService roleService;
    private final SysUserMapper userMapper;
    
    @Transactional(rollbackFor = Exception.class)
    public void insertUser(SysUserBo bo) {
        // 业务逻辑
    }
}
```

### 3.3 Mapper 层

**职责：**
- 数据库操作
- 动态 SQL
- 数据映射

**规范：**
```java
@Mapper
public interface SysUserMapper extends BaseMapperPlus<SysUserMapper, SysUser, SysUserVo> {
}
```

### 3.4 Entity/Domain 层

**职责：**
- 数据模型定义
- 数据库表映射
- 基础字段继承

**规范：**
```java
@Data
@EqualsAndHashCode(callSuper = true)
@TableName("sys_user")
public class SysUser extends TenantEntity {
    @TableId(value = "user_id")
    private Long userId;
    private String userName;
    // ...
}
```

---

## 四、缓存策略

### 4.1 缓存架构

```
应用层 → Redisson 客户端 → Redis 集群
           ↓
      本地缓存 (Caffeine)
```

### 4.2 缓存使用场景

| 数据类型 | 缓存策略 | 过期时间 |
|----------|----------|----------|
| 字典数据 | 永久缓存 | 无过期 |
| 参数配置 | 永久缓存 | 无过期 |
| 菜单权限 | 会话缓存 | 随 Token |
| 用户信息 | 会话缓存 | 随 Token |
| 验证码 | 临时缓存 | 5 分钟 |
| 限流计数 | 临时缓存 | 1 秒/1 分钟 |

### 4.3 缓存注解

```java
// 缓存读取
@Cacheable(cacheNames = "sys_dict", key = "#dictType")

// 缓存更新
@CachePut(cacheNames = "sys_dict", key = "#dict.dictType")

// 缓存删除
@CacheEvict(cacheNames = "sys_dict", key = "#dictType")
```

---

## 五、安全设计

### 5.1 认证流程

```
用户登录 → 验证账号密码 → 生成 JWT Token → 返回 Token
          ↓
后续请求 → 携带 Token → 验证 Token → 解析用户信息 → 处理请求
```

### 5.2 权限校验

```java
// 登录校验
@SaCheckLogin
// 角色校验
@SaCheckRole("admin")
// 权限校验
@SaCheckPermission("system:user:add")
// 二级认证
@SaCheckSafe()
```

### 5.3 数据加密

| 数据类型 | 加密方式 |
|----------|----------|
| 密码 | BCrypt |
| 敏感数据 | AES |
| 接口传输 | AES + RSA |

### 5.4 XSS 防护

- 全局 XSS 过滤器
- 参数自动转义
- 富文本白名单过滤

### 5.5 CSRF 防护

- Token 验证机制
- SameSite Cookie 策略

---

## 六、性能优化

### 6.1 数据库优化

- 索引优化
- 分页查询优化
- 批量操作
- 读写分离（可选）

### 6.2 缓存优化

- 热点数据缓存
- 缓存预热
- 缓存穿透防护
- 缓存雪崩防护

### 6.3 接口优化

- 接口限流
- 异步处理
- 批量接口
- 数据压缩

### 6.4 前端优化

- 静态资源 CDN
- 懒加载
- 虚拟滚动
- 请求合并

---

## 七、部署方案

### 7.1 单机部署

```
Nginx → Spring Boot (Jar) → MySQL/Redis/MinIO
```

### 7.2 集群部署

```
Nginx (负载均衡)
    ↓
┌───────────┬───────────┬───────────┐
│  Node 1   │  Node 2   │  Node 3   │
│ Spring    │ Spring    │ Spring    │
│ Boot      │ Boot      │ Boot      │
└───────────┴───────────┴───────────┘
    ↓           ↓           ↓
┌───────────────────────────────────┐
│         Redis 集群                 │
└───────────────────────────────────┘
    ↓
┌───────────────────────────────────┐
│         MySQL 主从                 │
└───────────────────────────────────┘
```

### 7.3 Docker 部署

提供 docker-compose.yml 一键部署：
- nginx
- mysql
- redis
- minio
- ruoyi-server

### 7.4 Kubernetes 部署

- Deployment 配置
- Service 配置
- ConfigMap 配置
- HPA 自动扩缩容

---

**文档结束**
