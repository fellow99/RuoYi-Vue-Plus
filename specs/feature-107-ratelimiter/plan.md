# feature-107-ratelimiter 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-107-ratelimiter | 最后更新：2026-08-10

## 1. 技术上下文
- 限流引擎：Redisson RRateLimiter 令牌桶算法（内置 Lua 脚本在 Redis 服务端原子执行）
- Redis 配置：redisConfig.setUseScriptCache(true) 缓存 Redisson Lua 脚本
- SpEL 动态 Key：MethodBasedEvaluationContext + BeanFactoryResolver 解析
- 限流 Key 格式：{keyPrefix}global:rate_limit:{requestURI}:{ip|clientId}:{spelKey}
- RateType 映射：DEFAULT → OVERALL，CLUSTER → PER_CLIENT

## 2. 模块结构
- ruoyi-common-redis: 3 个核心文件 + 1 个枚举（annotation + aspectj + config + enums）
- RateLimiterAspect：@Before 拦截，调用 RedisUtils.rateLimiter()，返回 -1 时抛出异常
- RateLimiterConfig：@AutoConfiguration(after = RedisConfiguration.class)
- 支持类：RedisUtils（rateLimiter 方法）、GlobalConstants（RATE_LIMIT_KEY）、KeyPrefixHandler（key 前缀）

## 3. 注解参数

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| key | String | "" | 限流 Key（支持 SpEL 如 #{#code}） |
| time | int | 60 | 限流时间窗口（秒） |
| count | int | 100 | 时间窗口内允许请求数 |
| limitType | LimitType | DEFAULT | 限流类型（DEFAULT/IP/CLUSTER） |
| message | String | "{rate.limiter.message}" | 国际化提示消息 Key |
| timeout | int | 86400 | 策略存活时间（秒），超时自动清除 |

## 4. 限流类型说明

| LimitType | Key 组合 | RateType | 适用场景 |
|-----------|----------|----------|----------|
| DEFAULT | URI + SpEL Key | OVERALL | 全局接口限流 |
| IP | URI + 客户端 IP + SpEL Key | OVERALL | 按 IP 限流（防止爬虫） |
| CLUSTER | URI + Redisson 实例 ID + SpEL Key | PER_CLIENT | 多实例场景每节点限流 |

## 5. 控制器清单（使用方）

| Controller | 接口 | 限流策略 |
|------------|------|----------|
| CaptchaController | /captcha/* | IP限流，防验证码暴力请求 |
| RedisRateLimiterController | /demo/rateLimiter | Demo 演示三种限流类型 |

## 6. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-redis/annotation/RateLimiter.java | @RateLimiter 注解定义 |
| ruoyi-common-redis/aspectj/RateLimiterAspect.java | 限流切面（@Before 令牌扣减） |
| ruoyi-common-redis/config/RateLimiterConfig.java | 自动配置（注册切面 bean） |
| ruoyi-common-redis/enums/LimitType.java | 限流类型枚举（DEFAULT/IP/CLUSTER） |
| ruoyi-common-redis/utils/RedisUtils.java | rateLimiter() 方法（Redisson RRateLimiter） |
| ruoyi-common-redis/config/RedisConfig.java | setUseScriptCache(true) 配置 |
| ruoyi-common-redis/handler/KeyPrefixHandler.java | Redis Key 前缀处理 |
| ruoyi-common-core/constant/GlobalConstants.java | RATE_LIMIT_KEY = "global:rate_limit:" |
| ruoyi-admin/resources/i18n/messages.properties | rate.limiter.message 国际化 |
| ruoyi-modules/ruoyi-demo/controller/RedisRateLimiterController.java | Demo 演示 |
