# 数据脱敏功能规格 (spec.md)

> 模块：feature-105-sensitive | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
提供声明式数据脱敏能力，在 Jackson 序列化 JSON 响应时自动对敏感字段进行掩码处理。通过 @Sensitive 注解标记字段并指定脱敏策略（如身份证、手机号、银行卡等），支持基于角色和权限的条件脱敏。

### 1.2 范围
- ✅ 18 种脱敏策略：身份证、手机号、地址、邮箱、银行卡、中文名、固定电话、用户ID、密码、IPv4、IPv6、车牌、首字符掩码、通用字符串掩码、高安全掩码、清空、清空为NULL
- ✅ 基于角色/权限的条件脱敏（roleKey / perms）
- ✅ Jackson 序列化时自动处理（ResponseBodyAdvice + JsonValueEnhancer + SensitiveJsonFieldProcessor）
- ✅ SPI扩展接口（SensitiveService）
- ✅ 超级管理员自动豁免脱敏
- ✅ Demo 示例（TestSensitiveController）

## 2. 用户故事
- 作为**普通用户**，查看用户列表时手机号和邮箱被自动脱敏
- 作为**管理人员**（拥有 "system:user:edit" 权限），可以看到完整的手机号和邮箱
- 作为**超级管理员**，所有敏感字段均不脱敏

## 3. 功能需求
- FR-105-001: 系统 MUST 支持 18 种脱敏策略，覆盖常见敏感数据类型
- FR-105-002: 系统 MUST 支持基于角色和权限的条件脱敏
- FR-105-003: 系统 MUST 在 Jackson 序列化阶段自动处理，无需业务代码介入
- FR-105-004: 系统 SHOULD 支持通过 SensitiveService SPI 自定义脱敏规则
- FR-105-005: 超级管理员 MUST 自动豁免脱敏

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| Sensitive | 字段级注解 (strategy + roleKey + perms) |
| SensitiveStrategy | 脱敏策略枚举（18种内置策略） |
| SensitiveService | 脱敏规则决策 SPI 接口 |
| SensitiveJsonFieldProcessor | Jackson 序列化处理器（order=100） |

## 5. 依赖
- ruoyi-common-json (JsonValueEnhancer + JsonFieldProcessor SPI)
- ruoyi-common-web (ResponseEnhancementAdvice)
- Hutool (DesensitizedUtil 基础脱敏实现)
- ruoyi-common-core (DesensitizedUtils 自定义脱敏工具)
- Sa-Token (StpUtil 角色/权限判断)
