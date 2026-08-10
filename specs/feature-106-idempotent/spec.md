# 分布式幂等功能规格 (spec.md)

> 模块：feature-106-idempotent | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
基于 Redis 实现分布式幂等控制，防止表单重复提交。参考美团 GTIS（分布式业务全局唯一性防重系统）设计思路，通过 @RepeatSubmit 注解 + AOP 切面 + Redis SET NX EX 原子操作实现请求级别的去重。

### 1.2 范围
- ✅ 方法级 @RepeatSubmit 注解声明幂等控制
- ✅ 可配置防重时间窗口（默认 5000ms）
- ✅ 基于请求 URI + 请求体 MD5 的幂等 Key 生成
- ✅ Redis 原子操作（RBucket.setIfAbsent）防并发
- ✅ 支持国际化错误消息
- ✅ 失败自动释放锁（@AfterThrowing 删除 Redis Key）
- ✅ 成功后保持锁定直到时间窗口过期

## 2. 用户故事
- 作为**用户**，我在短时间内连续点击提交按钮，系统只处理第一次请求
- 作为**开发者**，我可以为关键写操作添加 @RepeatSubmit 注解防止重复提交

## 3. 功能需求
- FR-106-001: 系统 MUST 支持声明式幂等控制（@RepeatSubmit 注解）
- FR-106-002: 系统 MUST 基于 Redis 原子操作实现分布式防重
- FR-106-003: 系统 SHOULD 支持可配置的防重时间窗口
- FR-106-004: 系统 SHOULD 支持 SpEL 表达式自定义幂等 Key
- FR-106-005: 请求失败时 MUST 自动释放幂等锁

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| RepeatSubmit | 方法级注解（interval/timeUnit/message） |
| RepeatSubmitAspect | AOP 切面，@Before 判断 + @AfterReturning/@AfterThrowing 清理 |
| IdempotentConfig | 自动配置类，注册 RepeatSubmitAspect bean |

## 5. 依赖
- ruoyi-common-redis (RedisUtils.setObjectIfAbsent)
- Redisson (RBucket 原子操作)
- Sa-Token (Token 获取，用于幂等 Key 生成)
- ruoyi-common-core (GlobalConstants.REPEAT_SUBMIT_KEY)
