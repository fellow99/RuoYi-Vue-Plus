# 短信服务功能规格 (spec.md)

> 模块：feature-108-sms | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
集成 SMS4J 短信融合框架，提供统一的多厂家短信发送接口，支持阿里云、腾讯云等数十种短信服务商，通过 YAML 配置即可切换厂家，支持多厂家共用。

### 1.2 解决的问题
- 短信服务商 API 各异，切换成本高
- 需要统一的短信发送、黑名单、重试拦截接口
- 框架需要内置短信异常处理和缓存支持

### 1.3 范围
- ✅ SMS4J 3.3.5 集成（多厂家短信融合）
- ✅ Redis 缓存支持（PlusSmsDao，统一使用框架 RedisUtils）
- ✅ 短信异常全局处理（SmsExceptionHandler）
- ✅ Demo 演示（阿里云/腾讯云发送、黑名单管理）
- ❌ 短信模板管理（由短信服务商控制台管理）

## 2. 用户故事
- 作为**开发人员**，我可以通过统一的 SmsFactory API 调用不同短信服务商
- 作为**运维人员**，我可以在 YAML 中配置多个短信厂家并自由切换
- 作为**管理员**，我可以将手机号加入黑名单防止短信轰炸

## 3. 功能需求
- FR-108-001: 系统 MUST 支持 SMS4J 多厂家短信发送（至少阿里云、腾讯云）
- FR-108-002: 系统 MUST 支持通过 Redis 缓存短信重试和拦截状态
- FR-108-003: 系统 MUST 全局捕获 SmsBlendException 并返回友好错误
- FR-108-004: 系统 SHOULD 支持短信黑名单管理（添加/移除）

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| SmsBlend (SMS4J) | 短信融合接口，通过 SmsFactory.getSmsBlend(configId) 获取 |
| SmsResponse (SMS4J) | 短信发送响应对象 |
| PlusSmsDao | 框架自定义 SmsDao 实现，基于 RedisUtils 缓存 |
| SmsExceptionHandler | 全局 SmsBlendException 处理器 |

## 5. 配置示例

```yaml
sms:
  # 短信服务商配置（SMS4J 多厂家）
  config1:    # 阿里云
    supplier: alibaba
    accessKeyId: your-access-key
    accessKeySecret: your-access-secret
    signature: 您的签名
  config2:    # 腾讯云
    supplier: tencent
    accessKeyId: your-access-key
    accessKeySecret: your-access-secret
    signature: 您的签名
```

## 6. 依赖
- SMS4J 3.3.5 (sms4j-spring-boot-starter)
- ruoyi-common-redis（PlusSmsDao 缓存实现）
- ruoyi-common-core（基础工具类）
