# 数据加解密功能规格 (spec.md)

> 模块：feature-104-encrypt | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
提供两层数据加解密能力：(1) API 传输加密——基于动态 AES + RSA 的请求/响应体加密，每次请求使用独立密钥；(2) 数据库字段加解密——通过 @EncryptField 注解 + MyBatis 拦截器在存取数据库时自动加解密，支持 BASE64/AES/RSA/SM2/SM4 五种算法。

### 1.2 范围
- ✅ API 传输加密（@ApiEncrypt 注解控制响应加密）
- ✅ 动态 AES 密钥 + RSA 加密密钥传输
- ✅ CryptoFilter 拦截请求/响应体加解密
- ✅ 数据库字段加密（@EncryptField 注解）
- ✅ MyBatis 加密拦截器（MybatisEncryptInterceptor，INSERT/UPDATE 时自动加密）
- ✅ MyBatis 解密拦截器（MybatisDecryptInterceptor，SELECT 时自动解密）
- ✅ 五种加密算法：BASE64、AES、RSA、SM2（国密）、SM4（国密）
- ✅ 两种编码方式：BASE64、HEX
- ✅ EncryptorManager 管理加密器实例
- ✅ EncryptContext/EncryptContextFactory 加密上下文管理

## 2. 用户故事
- 作为**开发者**，我可以在方法上添加 @ApiEncrypt 注解自动加密 API 响应
- 作为**开发者**，我可以在实体字段上添加 @EncryptField 注解，ORM 自动加解密
- 作为**安全管理员**，我可以配置国密算法（SM2/SM4）满足合规要求

## 3. 功能需求
- FR-104-001: 系统 SHOULD 支持 API 传输加密（动态 AES + RSA）
- FR-104-002: 系统 MUST 支持数据库字段自动加解密（@EncryptField 注解）
- FR-104-003: 系统 MUST 支持 BASE64 / AES / RSA 三种算法
- FR-104-004: 系统 SHOULD 支持国密 SM2 / SM4 算法
- FR-104-005: 系统 MUST 支持多种编码方式（BASE64 / HEX）
- FR-104-006: 加密算法可配置，支持 DEFAULT 走全局 YAML 配置

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| ApiEncrypt | 方法级注解，控制 API 响应加密 |
| EncryptField | 字段级注解，标记需加解密的数据列 |
| AlgorithmType | 算法枚举（DEFAULT / BASE64 / AES / RSA / SM2 / SM4） |
| EncodeType | 编码枚举（DEFAULT / BASE64 / HEX） |
| IEncryptor | 加密器接口（encrypt/decrypt） |
| EncryptContext | 加密上下文（算法+密钥+编码） |

## 5. 依赖
- Hutool（对称/非对称加密工具）
- BouncyCastle（SM2/SM4 国密算法）
- MyBatis-Plus（拦截器集成）
- Spring Boot AutoConfiguration
- ruoyi-common-json（序列化支持）
