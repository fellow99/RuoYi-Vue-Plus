# 分布式限流功能规格 (spec.md)

> 模块：feature-107-ratelimiter | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
基于 Redis (Redisson RRateLimiter) 提供分布式限流能力，支持全局限流、IP 限流、集群实例限流三种策略，通过 @RateLimiter 注解声明式使用，支持 SpEL 表达式动态 Key 解析。

### 1.2 范围
- ✅ 方法级 @RateLimiter 注解声明限流
- ✅ 三种限流类型：DEFAULT（全局限流）、IP（IP限流）、CLUSTER（集群实例限流）
- ✅ 基于 Redisson RRateLimiter 令牌桶算法
- ✅ SpEL 表达式支持动态限流 Key
- ✅ 可配置时间窗口、令牌数、策略超时
- ✅ 支持国际化限流提示消息
- ✅ Demo 演示（RedisRateLimiterController）

## 2. 用户故事
- 作为**运维人员**，我可以限制短信验证码接口每分钟 5 次调用
- 作为**管理员**，我可以为不同业务接口配置不同的限流策略
- 作为**开发者**，我可以通过 @RateLimiter 注解一键限流

## 3. 功能需求
- FR-107-001: 系统 MUST 支持分布式限流（Redis 令牌桶算法）
- FR-107-002: 系统 MUST 支持三种限流维度（全局/IP/集群实例）
- FR-107-003: 系统 SHOULD 支持 SpEL 表达式动态限流 Key
- FR-107-004: 系统 MUST 在令牌不足时返回限流提示消息
- FR-107-005: 限流策略超时后自动清除（默认 86400s）

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| RateLimiter | 方法级注解（key/time/count/limitType/message/timeout） |
| LimitType | 限流类型枚举（DEFAULT/IP/CLUSTER） |
| RateLimiterAspect | AOP 切面，@Before 扣令牌 |
| RateLimiterConfig | 自动配置类 |

## 5. 依赖
- ruoyi-common-redis (RedisUtils.rateLimiter)
- Redisson (RRateLimiter 令牌桶 + 内置 Lua 脚本)
- Spring EL (SpEL 表达式动态 Key 解析)
- ruoyi-common-core (GlobalConstants.RATE_LIMIT_KEY)
