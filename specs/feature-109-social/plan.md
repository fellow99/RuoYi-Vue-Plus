# feature-109-social 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-109-social | 最后更新：2026-08-10

## 1. 技术上下文
- JustAuth 3.0.1 (io.github.windtool:JustAuth), JDK 21, Spring Boot 4.1
- 配置前缀: justauth，YAML 驱动平台配置

## 2. 宪法合规
| 原则 | 状态 |
|------|------|
| 插件化设计 | ✅ 独立模块 ruoyi-common-social，按需引入 |
| 分布式就绪 | ✅ AuthRedisStateCache 基于 RedisUtils，集群共享授权状态 |

## 3. 模块结构
- ruoyi-common/ruoyi-common-social: 5 个文件
  - config/SocialAutoConfiguration.java — 注册 AuthRedisStateCache Bean
  - config/properties/SocialProperties.java — @ConfigurationProperties(prefix="justauth")，type Map
  - config/properties/SocialLoginConfigProperties.java — 单平台配置（clientId/secret/redirectUri/scopes 等）
  - utils/SocialUtils.java — 静态工具类，根据 source 构建 AuthRequest（switch 匹配 24 种平台）
  - utils/AuthRedisStateCache.java — JustAuth AuthStateCache 接口 Redis 实现

## 4. 接口契约
- SysSocialController (/system/social):
  - GET /system/social/list — 查询当前登录用户的社会化账号绑定列表 (SysSocialVo)

## 5. 授权流程
```
前端 → 第三方授权页 → 回调 /auth/{source}/callback
    → SocialUtils.loginAuth(source, code, state, socialProperties)
        → AuthRedisStateCache (Redis 缓存 state)
        → AuthRequest.login(callback)
        → 返回 AuthUser → 系统创建/绑定用户 → 返回 Token
```

## 6. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-social/pom.xml | 依赖 JustAuth, ruoyi-common-redis |
| ruoyi-common-social/.../config/SocialAutoConfiguration.java | 注册 AuthRedisStateCache Bean |
| ruoyi-common-social/.../config/properties/SocialProperties.java | justauth.type 配置映射 |
| ruoyi-common-social/.../config/properties/SocialLoginConfigProperties.java | 单平台配置属性（22 个字段） |
| ruoyi-common-social/.../utils/SocialUtils.java | 构建 AuthRequest（switch 匹配 24 种平台） |
| ruoyi-common-social/.../utils/AuthRedisStateCache.java | Redis 授权状态缓存（默认 3 分钟过期） |
| ruoyi-modules/ruoyi-system/.../controller/system/SysSocialController.java | 社交绑定查询 |
| ruoyi-modules/ruoyi-system/.../domain/SysSocial.java | 社交账号绑定实体 |
| ruoyi-modules/ruoyi-system/.../service/ISysSocialService.java | 社交账号服务接口 |
